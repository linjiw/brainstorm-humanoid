
# Learning Guide: Expressive-Humanoid Repository

## Technical Brief

The **expressive-humanoid** repository implements state-of-the-art reinforcement learning techniques to achieve expressive whole-body control for humanoid robots. This approach combines advanced imitation learning methods with robust reinforcement learning to enable humanoids to perform natural, human-like movements while maintaining balance and locomotion capabilities.

**Key Technical Components:**
- **Adversarial Motion Priors (AMP)**: Enables policies to learn from motion capture data by training a discriminator to distinguish between robot-generated motion and reference motion
- **Adversarial Skill Embeddings (ASE)**: Extends AMP by learning a latent skill space for more controllable expressive behaviors
- **Isaac Gym**: Provides fast, GPU-accelerated physics simulation for parallel RL training
- **Proximal Policy Optimization (PPO)**: Core RL algorithm offering stability and performance for continuous control
- **Domain Randomization**: Enhances sim-to-real transfer by varying simulation parameters during training

> **Relevance to Whole-Body Control**: This repository specifically addresses the challenge of combining upper-body expressivity with lower-body locomotion stability - a core problem in whole-body humanoid control. The approach allows for natural, coordinated movements across the entire robot structure.

## Repository Structure and Components

### 1. Core Technologies & Overall Goal

- **Goal**: Train control policies for humanoid robots (like H1) to perform expressive and dynamic motions that mimic human movement patterns
- **Simulator**: Uses Isaac Gym via the legged_gym component for fast, parallelized physics simulation
- **RL Framework**: Custom RL library (rsl_rl) with Proximal Policy Optimization (PPO)
- **Key Techniques**: Advanced imitation learning with Adversarial Motion Priors (AMP) and Adversarial Skill Embeddings (ASE)

### 2. Project Structure Overview

```
expressive-humanoid/
├── README.md                       # Primary starting point with setup instructions
├── legged_gym/                     # Simulation environments
│   ├── envs/
│   │   ├── base/                   # Base classes for legged robots and tasks
│   │   └── h1/                     # H1 humanoid robot implementation
│   │       ├── h1_config.py        # Robot parameters and physics settings
│   │       ├── h1_amp.py           # AMP-specific environment setup
│   │       └── h1_mimic_amp.py     # Mimic-based AMP environment setup
│   └── scripts/
│       ├── train.py                # Training script
│       └── play.py                 # Policy playback script
├── rsl_rl/                         # RL library
│   ├── algorithms/
│   │   ├── ppo.py                  # PPO implementation
│   │   └── ppo_mimic.py            # PPO variant for imitation learning
│   ├── modules/
│   │   ├── actor_critic.py         # Policy and value networks
│   │   └── amp_discriminator.py    # AMP discriminator network
│   └── runners/
│       ├── on_policy_runner.py     # Standard runner
│       └── on_policy_runner_mimic_amp.py  # AMP-specific training runner
└── ASE/                            # Adversarial Skill Embeddings
    ├── ase/learning/               # Learning algorithms
    │   ├── amp_agent.py            # AMP agent implementation
    │   ├── ase_agent.py            # ASE agent implementation
    │   └── hrl_agent.py            # Hierarchical RL agent
    ├── ase/env/tasks/              # Task-specific implementations
    ├── ase/poselib/                # Motion capture data processing
    │   ├── retarget_motion_h1.py   # Motion retargeting for H1 robot
    │   └── parse_cmu_mocap_all.py  # CMU mocap data parsing
    ├── ase/utils/
    │   └── motion_lib.py           # Motion library management
    └── ase/data/cfg/               # Configuration files
        ├── amp_humanoid.yaml       # AMP configuration
        └── ase_humanoid.yaml       # ASE configuration
```

### 3. Understanding the RL Approach

> **Critical for Whole-Body Control**: The RL implementation combines task-based rewards with imitation learning to achieve both functional objectives and natural motion.

#### Algorithm: PPO (Proximal Policy Optimization)
- On-policy algorithm with stability for continuous control
- Implemented in `rsl_rl/algorithms/ppo.py`

#### Imitation Learning via AMP
- **Core concept**: Uses a discriminator network to distinguish between robot-generated motion and reference motion
- Policy receives reward based on "fooling" the discriminator
- Implementation in `rsl_rl/modules/amp_discriminator.py`
- Configuration in `ASE/ase/data/cfg/amp_humanoid.yaml`

#### Adversarial Skill Embeddings (ASE)
- Extends AMP by learning a latent "skill embedding" space
- Maps high-level commands to specific motion styles
- Enables more complex and controllable behaviors
- Potentially uses hierarchical structures (indicated by `hrl_agent.py`)

### 4. Sim-to-Real Transfer Techniques

> **Essential for Real-World Deployment**: These techniques are crucial for transferring whole-body control policies from simulation to real robots.

#### Domain Randomization (DR)
- Varies simulation parameters during training (mass, friction, motor parameters)
- Forces policy robustness to uncertainties present in the real world
- Configuration in `legged_gym/envs/h1/h1_config.py`

#### High-Fidelity Simulation
- Isaac Gym provides fast, GPU-accelerated physics simulation
- Reduces the "reality gap" between simulation and real world

#### Motion Priors (AMP/ASE)
- Training with human motion capture data grounds learned behaviors in physically plausible movements
- Improves transfer to real robots by enforcing natural motion patterns

#### Robust Policy Learning
- PPO algorithm with noise injection for learning policies less sensitive to sim/real differences
- Explicit handling of observation and actuation delays

## Implementation Recommendations for Whole-Body Control

1. **Motion Dataset Preparation**: Carefully curate motion capture data that includes both upper-body expressivity and lower-body locomotion
2. **Reward Balancing**: Find appropriate balance between task rewards and style/imitation rewards
3. **Hierarchical Structure**: Consider separate but coordinated control for lower body (stability) and upper body (expressivity)
4. **Training Curriculum**: Start with simpler tasks and gradually increase complexity
5. **Domain Randomization**: Pay special attention to parameters affecting whole-body dynamics

---

For more detailed information on whole-body control using reinforcement learning, refer to papers like "Expressive Whole-Body Control for Humanoid Robots" (2024) by Xuxin Cheng et al. and "Real-World Humanoid Locomotion with Reinforcement Learning" (2023) by Ilija Radosavovic et al.
