# Humanoid Robot Whole Body Control using Reinforcement Learning: Reading List

## Introduction to the Field

This reading list organizes key research papers and resources on whole body control of humanoid robots using reinforcement learning. The papers are grouped by topic to help guide your study through this quickly evolving field.

## Foundational Papers

### Expressive Whole-Body Control
- **Expressive Whole-Body Control for Humanoid Robots** (2024)
  - *Xuxin Cheng et al.*
  - [Paper](https://arxiv.org/abs/2402.16796) | [Project Page](https://expressive-humanoid.github.io/)
  - Summary: Introduces ExBody, a novel approach that combines large-scale human motion capture data with deep reinforcement learning to enable humanoid robots to perform expressive movements with their upper body while maintaining robust locomotion.

### Real-World Humanoid Locomotion
- **Real-World Humanoid Locomotion with Reinforcement Learning** (2023)
  - *Ilija Radosavovic et al.*
  - [Paper](https://arxiv.org/abs/2303.03381) | [Project Page](https://learning-humanoid-locomotion.github.io/)
  - Summary: Presents a fully learning-based approach using a causal Transformer model trained with model-free reinforcement learning that can be deployed to real-world humanoid robots zero-shot.

### Humanoid Locomotion with Human Reference
- **Whole-body Humanoid Robot Locomotion with Human Reference** (2024)
  - [Paper](https://arxiv.org/abs/2402.18294)
  - Summary: Presents a humanoid robot named Adam and a new whole-body imitation learning framework that reduces the Sim2Real gap for humanoid robots.

## Surveys and Overview Papers

- **Humanoid Locomotion and Manipulation: Current Progress and Challenges in Control, Planning, and Learning** (2024)
  - *Zhaoyuan Gu et al.*
  - [Paper](https://arxiv.org/abs/2501.02116)
  - Summary: Comprehensive survey covering model-based and learning-based approaches for humanoid locomotion and manipulation, discussing challenges and future trends.

- **Deep Reinforcement Learning for Bipedal Locomotion: A Brief Survey** (2024)
  - [Paper](https://arxiv.org/abs/2404.17070)
  - Summary: Systematically categorizes DRL frameworks for bipedal locomotion into end-to-end and hierarchical control schemes.

- **Deep Reinforcement Learning for Robotics: A Survey of Real-World Successes** (2024)
  - [Paper](https://arxiv.org/abs/2408.03539)
  - Summary: Analyzes successful applications of deep reinforcement learning across various robotics domains, including sections on locomotion.

## Specialized Techniques

### Efficient Reinforcement Learning
- **Efficient Reinforcement Learning for Humanoid Whole-Body Control** (2016)
  - [Paper](https://ieeexplore.ieee.org/document/7803348/)
  - Summary: Shows how the efficiency of reinforcement learning for humanoid control can be improved through Bayesian optimization.

### Challenging Terrain Navigation
- **Learning Humanoid Locomotion over Challenging Terrain** (2024)
  - [Paper](https://arxiv.org/abs/2410.03654)
  - Summary: Proposes a two-step training approach combining pre-training with sequence modeling and fine-tuning with reinforcement learning to enable humanoid locomotion over difficult terrains.

### Biomechanics Integration
- **Deep Reinforcement Learning for Modeling Human Locomotion Control in Neuromechanical Simulation** (2021)
  - [Paper](https://jneuroengrehab.biomedcentral.com/articles/10.1186/s12984-021-00919-y)
  - Summary: Explores the application of deep reinforcement learning to neuromechanical simulation for modeling human motor control.

## Resource Collections

- **Awesome Humanoid Learning** - GitHub Repository
  - *jonyzhang2023*
  - [Repository](https://github.com/jonyzhang2023/awesome-humanoid-learning)
  - Summary: A curated collection of resources related to humanoid robot learning, including papers on locomotion, manipulation, and whole-body control.

## Implementation Guidelines

When implementing reinforcement learning for humanoid robot whole body control, consider these key aspects:

1. **Sim-to-Real Transfer**: Most successful approaches first train in simulation and then transfer to real robots.
2. **Motion Capture Integration**: Human motion data can significantly improve the naturalness of movements.
3. **Hierarchical Control**: Consider separating upper and lower body control for more robust performance.
4. **Transformer-Based Models**: Recent approaches using transformer architectures show strong capabilities for context adaptation.
5. **Terrain Adaptability**: Robust controllers need to handle a variety of terrains and disturbances.

## Recent Trends

- Integration of large language models for higher-level task planning
- Combining model-based and learning-based approaches
- Using transformer architectures to improve in-context adaptation
- Whole-body tactile sensing to enhance physical interactions
- Pre-training on human data followed by reinforcement learning fine-tuning

## Recommended Reading Order

For newcomers to the field, we recommend the following order:
1. Start with the survey papers to get an overview
2. Read the Real-World Humanoid Locomotion paper for fundamentals
3. Explore the Expressive Whole-Body Control paper for more advanced concepts
4. Dive into specialized techniques based on your specific interests
