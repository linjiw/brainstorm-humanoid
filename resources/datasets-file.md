# Datasets for Humanoid Robotics Research

This page lists datasets that are useful for research in humanoid robotics, particularly for training and evaluating reinforcement learning and imitation learning algorithms.

## Motion Capture Datasets

### Human Motion

- **Human3.6M**
  - [Website](http://vision.imar.ro/human3.6m/description.php)
  - 3.6 million human poses and corresponding images recorded from 5 female and 6 male subjects under 4 different viewpoints
  - Includes 17 scenarios: discussion, smoking, taking photos, talking on the phone, etc.

- **AMASS (Archive of Motion Capture as Surface Shapes)**
  - [Website](https://amass.is.tue.mpg.de/)
  - Large database of human motion unifying different optical marker-based motion capture datasets by representing them in a common framework
  - Contains 40 hours of motion data from 300+ subjects

- **CMU Graphics Lab Motion Capture Database**
  - [Website](http://mocap.cs.cmu.edu/)
  - Contains hundreds of motion clips of various activities performed by human subjects
  - Widely used in animation and robotics research

### Humanoid Robot Motion

- **DexMV: Dexterous Manipulation Video Dataset**
  - [Website](https://dexmv.github.io/)
  - Videos of dexterous manipulation tasks performed by humans
  - Useful for imitation learning in humanoid manipulation

## Reinforcement Learning Environments

- **Gymnasium Humanoid Environments**
  - [GitHub](https://github.com/Farama-Foundation/Gymnasium)
  - Standard benchmark environments for training humanoid control policies
  - Includes Humanoid-v4 and HumanoidStandup-v4

- **DeepMind Control Suite**
  - [GitHub](https://github.com/deepmind/dm_control)
  - Includes humanoid locomotion tasks of varying difficulty
  - Built on the MuJoCo physics engine

- **RoboSchool/PyBullet Gym**
  - [GitHub](https://github.com/openai/roboschool)
  - Open-source alternatives to MuJoCo-based environments
  - Includes various humanoid tasks

## Datasets for Sim-to-Real Transfer

- **Real Robot Challenge**
  - [Website](https://real-robot-challenge.com/)
  - Datasets from real robotic manipulation tasks
  - Useful for validating sim-to-real transfer methods

## Human-Robot Interaction Datasets

- **H2O: Human-to-humanOid Dataset**
  - Contains paired human and humanoid motions
  - Useful for learning mapping functions between human and robot motion

## Contributing

If you know of other datasets that should be included here, please open an issue or submit a pull request following the contribution guidelines.
