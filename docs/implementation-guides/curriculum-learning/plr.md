# Introduction to Prioritized Level Replay (PLR)

Reinforcement learning (RL) agents often struggle to generalize beyond their training experiences, especially in environments with procedurally generated content (PCG) where each "level" is a unique instance. Standard training methods typically sample these levels uniformly. However, the learning potential of a level depends on the agent's current policy.   

Prioritized Level Replay (PLR) is a framework designed to improve sample efficiency and generalization in RL by moving away from uniform level sampling. Instead of picking the next training level randomly, PLR selectively samples levels, prioritizing those estimated to offer the highest learning potential if revisited. It essentially creates an emergent curriculum by focusing on levels that are appropriately challenging for the agent's current capabilities.   

PLR is designed to be a general method that can be combined with various RL algorithms and doesn't require control over the level generation process itself, only the ability to replay a specific level using its identifier (like a seed).   

## How PLR Works

PLR modifies the experience collection part of the RL training loop. Here's a breakdown of its core mechanics:

### Level Sampling Decision
At the start of collecting new experiences, PLR decides whether to:

- **Sample a New, Unseen Level**: Pick a level from the training set that the agent hasn't encountered before. The probability of doing this often decreases as more levels are seen.   
- **Replay a Seen Level**: Select a level the agent has already visited, sampled from a special "replay distribution" $P_{replay}$.   

### The Replay Distribution ($P_{replay}$)
This distribution determines which previously seen level to replay. It's a mix of two components:   

- **Score-based Prioritization ($P_S$)**: Prioritizes levels based on their estimated future learning potential. Levels with higher scores are more likely to be selected.   
- **Staleness-based Prioritization ($P_C$)**: Prioritizes levels that haven't been replayed recently. This prevents scores from becoming too "off-policy" as the agent learns and ensures diversity.   

The final probability is a weighted sum: $P_{replay} = (1−ρ) \cdot P_S + ρ \cdot P_C$, where $ρ$ is a "staleness coefficient" hyperparameter.   

### Scoring Levels ($S_i$)
After the agent completes an episode (or trajectory segment) $τ$ on a level $l_i$, PLR calculates a score $S_i$ to estimate the learning potential of replaying that level.   

- **TD-Errors as Proxy**: The core idea is to use Temporal Difference (TD) errors as a proxy for learning potential. High magnitude TD-errors suggest a discrepancy between the agent's value estimates and actual outcomes, indicating room for learning.   
- **GAE Magnitude / L1 Value Loss**: The paper finds that using the average magnitude of the Generalized Advantage Estimate (GAE) over the trajectory is particularly effective. This is often equivalent to the L1 value loss used in algorithms like PPO. The formula is: 
  
  $$S_i = \frac{1}{T} \sum_{t=0}^{T} \left|\sum_{k=t}^{T} (γλ)^{k−t} δ_k\right|$$
  
- **Value Correction Hypothesis**: In sparse reward settings, prioritizing levels with high value loss guides the agent towards "threshold levels" – those at the edge of its current capabilities – creating a natural curriculum.   

### Prioritization Schemes
How scores translate into probabilities in $P_S$:

- **Rank Prioritization**: Probabilities are based on the rank of a level's score ($h(S_i) = \frac{1}{rank(S_i)}$). This is often found to be more stable than using the raw scores directly.   
- **Proportional Prioritization**: Probabilities are directly proportional to the scores ($h(S_i) = S_i$).   

A temperature parameter $β$ can tune the sharpness of the distribution $P_S(l_i) \propto h(S_i)^{1/β}$.   

### Staleness Calculation ($P_C$)
This assigns probability mass proportional to how long ago a level was last sampled ($P_C(l_i) \propto c−C_i$), where $c$ is the current global episode count and $C_i$ is the episode count when level $l_i$ was last sampled.   

## Organization into "Code" (Based on Pseudocode)

While I cannot generate runnable Python code, the paper provides algorithms that show how PLR integrates into a typical RL loop.

### Algorithm 1: Policy-Gradient Training Loop with PLR    

**Initialization:**
- Initialize policy $π_θ$.
- Initialize empty lists/arrays for seen levels ($Λ_{seen}$), their scores ($S$), and last-visited timestamps ($C$).
- Initialize global episode counter $c$.

**Training Loop:**
1. **Experience Collection**: Instead of standard sampling, call a function like collect_experiences (which uses Algorithm 2 internally) to gather a batch of trajectories $B$.
2. **Policy Update**: Update the policy $θ$ using the collected batch $B$ (e.g., using PPO).

### Algorithm 2: Experience Collection with PLR    

**Input:** Training levels, $Λ_{seen}$, policy $π$, scores $S$, timestamps $C$, counter $c$.  
**Output:** A sampled trajectory $τ$.

**Steps:**
1. Increment global episode counter $c$.
2. **Decide Replay vs. New**: Sample $d \sim P_D(d)$.
   - If **New Level** ($d=0$ and unseen levels exist):
     - Sample $l_i$ uniformly from unseen levels ($Λ_{train} \setminus Λ_{seen}$).
     - Add $l_i$ to $Λ_{seen}$, initialize its score $S_i$ and timestamp $C_i$.
   - Else (**Replay Level**):
     - Calculate $P_S$ using current scores $S$ and prioritization method (e.g., rank-based).   
     - Calculate $P_C$ using timestamps $C$ and current count $c$.   
     - Form $P_{replay} = (1−ρ)P_S + ρP_C$.   
     - Sample level $l_i \sim P_{replay}$.
3. **Sample Trajectory**: Collect trajectory $τ$ by running policy $π$ on level $l_i$.
4. **Update Score & Timestamp**: Calculate the score $S_i = score(τ,π)$ (e.g., using Eq. 2). Update timestamp $C_i ← c$.   
5. Return $τ$.

(Note: Algorithms 3 & 4 in the paper adapt this for T-step rollouts instead of full episodes).   

## Plug-and-Play Module for Curriculum RL

PLR acts as a "plug-and-play" module by replacing the standard level sampling mechanism within an existing RL training framework.   

**Integration Point**: It primarily modifies the part of the code responsible for deciding which environment instance (level) to run the policy in to collect the next batch of experiences.   

### Requirements:
- **Identifiable Levels**: The environment must allow specifying and resetting to distinct levels using an identifier (e.g., a seed).   
- **Shared Dynamics**: Levels should share underlying dynamics or structure so learning on one can generalize to others.   
- **Scoring Mechanism**: The RL algorithm needs to provide the necessary information to compute the chosen score (e.g., value estimates $V(s)$ to calculate TD-errors or GAE). PPO with GAE is a common choice compatible with the L1 value loss score.   

### Implementation:
1. Maintain the data structures: $Λ_{seen}$ (list of seen level IDs), $S$ (list of scores), $C$ (list of timestamps).   
2. Implement the sampleNextLevel logic from Algorithm 2/4. This involves calculating $P_S$, $P_C$, mixing them into $P_{replay}$, sampling a level ID, and potentially adding new levels to the tracked lists.   
3. Implement the chosen score function (e.g., Eq. 2 using GAE magnitudes calculated during the policy update phase).   
4. Modify the training loop to call sampleNextLevel when a new episode/rollout begins in an environment worker/instance.   
5. Update the score $S_i$ and timestamp $C_i$ for the level $l_i$ after its trajectory $τ$ is collected.   

By selectively sampling levels based on learning potential and staleness, PLR adaptively focuses the agent's training on the most informative levels at any given time, effectively inducing a curriculum without needing explicit difficulty definitions.