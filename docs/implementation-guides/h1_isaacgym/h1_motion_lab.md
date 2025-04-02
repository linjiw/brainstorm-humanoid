Okay, let's break down how the Unitree H1 robot is handled in the ProtoMotions repository for full-body control, covering data processing, configuration, training, and testing.

1.  **Robot Configuration (`protomotions/config/robot/h1.yaml`, `protomotions/simulator/isaaclab/utils/robots.py`)**
    *   **Definition:** The H1 robot's physical properties, links, joints, and sensors are defined primarily through asset files (URDF for IsaacGym/CPU PhysX, MJCF for Genesis, and USD for IsaacLab). The paths are specified in `protomotions/config/robot/h1.yaml` (`asset_file_name`, `usd_asset_file_name`) and used in the simulator-specific setup like `protomotions/simulator/isaaclab/utils/robots.py`.
    *   **IsaacLab Specifics:** The `H1_CFG` in `protomotions/simulator/isaaclab/utils/robots.py` configures the H1 articulation within IsaacLab, setting simulation parameters like disabling self-collisions and specifying solver iterations. Notably, unlike SMPL(X), it doesn't define actuators directly in this config; they are handled via the main Hydra config.
    *   **Hydra Configuration (`h1.yaml`):** This is the central configuration hub for H1.
        *   **Kinematics:** Defines `body_names`, `dof_names`, `key_bodies` (like `left_foot_link`, `right_foot_link`, `left_arm_end_effector`), and identifies the head and feet.
        *   **Limits:** Specifies `dof_effort_limits`, `dof_vel_limits`, armatures, and frictions for each joint.
        *   **Initial State:** Sets the default starting position (`pos: [ 0.0, 0.0, 1.05 ]`) and `default_joint_angles`.
        *   **Control:** Specifies `control_type: proportional` (PD Control) and provides the `stiffness` and `damping` values for different joint groups (hip, knee, ankle, torso, shoulder, elbow).
        *   **Simulation:** Sets default simulation rates (`fps`, `decimation`) for different backends.
        *   **Motion Library:** Crucially, it overrides the default motion library to use `protomotions.utils.motion_lib_h1.H1_MotionLib`.

2.  **Data Processing (`README.md`, `data/scripts/convert_h1_to_isaac.py`, `protomotions/utils/motion_lib_h1.py`)**
    *   **Retargeting:** The core process involves taking motion capture data (like AMASS) and retargeting it to fit the H1's morphology and joint structure.
    *   **Scripts:** The `README.md` mentions using `data/scripts/convert_amass_to_isaac.py` with specific flags (`--robot-type=h1 --force-retarget`) which utilizes the Mink library. There's also a dedicated script, `data/scripts/convert_h1_to_isaac.py`, which appears specifically designed for converting data *to* the H1 format, processing AMASS `.npz` files.
    *   **Output Format:** The retargeting process generates `.npy` files containing the motion data adapted for H1 (e.g., `data/motions/h1_walk.npy`).
    *   **Packaging:** These `.npy` files might be further processed and packaged into `.pt` (PyTorch tensor) files using `data/scripts/package_motion_lib.py` for faster loading during training.
    *   **`H1_MotionLib`:** This specialized class in `protomotions/utils/motion_lib_h1.py` handles loading the H1-specific motion data (likely the `.pt` files). It indicates that DOF velocities are pre-computed and stored within these files. It also includes methods for slicing and potentially adjusting motion heights specifically for H1 data.

3.  **Training (`README.md`, `protomotions/train_agent.py`)**
    *   **Entry Point:** Training is launched using `protomotions/train_agent.py`.
    *   **Selection:** You select the H1 robot via the command line (`+robot=h1`) and choose the simulator (`+simulator=isaaclab`, `+simulator=isaacgym`, or `+simulator=genesis`).
    *   **Experiments:** Various pre-defined experiments (`+exp=...`) can be run with H1:
        *   `+exp=steering_mlp`: Trains a basic locomotion policy using PPO and task rewards only (no motion imitation). Example:
            ```bash
            PYTHON_PATH protomotions/train_agent.py +exp=steering_mlp +robot=h1 +simulator=isaaclab +experiment_name=h1_steering_isaaclab
            ```
        *   AMP-based experiments (`+exp=amp_mlp`, `+exp=path_follower_amp_mlp`) require providing an H1 motion file via `motion_file=data/motions/h1_walk.npy` (or another processed H1 motion).
    *   **Control:** The training process utilizes the PD control parameters (stiffness, damping) defined in `protomotions/config/robot/h1.yaml`.
    *   **Logging & Checkpoints:** Specify an `experiment_name` to organize logs and save model checkpoints (e.g., in `results/h1_steering_isaaclab/`). Training can be resumed automatically if an experiment with the same name exists.

4.  **Evaluation/Testing (`README.md`, `protomotions/eval_agent.py`)**
    *   **Entry Point:** Use `protomotions/eval_agent.py` to visualize and test trained policies.
    *   **Selection:** Specify the robot, simulator, and the path to the saved checkpoint:
        ```bash
        PYTHON_PATH protomotions/eval_agent.py +robot=h1 +simulator=isaaclab checkpoint=results/h1_steering_isaaclab/last.ckpt
        ```
        (You might need to add `motion_file=...` if the original experiment required it).
    *   **Interaction:** Use the provided keyboard controls (J, R, O, L, etc.) to interact with the simulation (apply forces, reset, change camera, record video).

In essence, the H1 is a first-class citizen in ProtoMotions, with dedicated configuration files, data retargeting pipelines, a specialized motion loading class, and clear examples for training and evaluation using the framework's core scripts and configuration system.
