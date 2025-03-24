# Actor-Critic with Boltzmann Exploration for Multi-Intersection Traffic Signal Control

## Table of Contents

1. [Introduction](#1-introduction)  
2. [Method](#2-method)  
3. [Experiments](#3-experiments)  
4. [Conclusions](#4-conclusions)

---

## 1. Introduction

In the era of intelligent transportation systems, coordinated control of multiple intersections plays a vital role in improving traffic efficiency and reducing congestion. Traditional fixed-time signal control strategies lack the adaptability to dynamic traffic conditions, motivating the development of **reinforcement learning** (RL) techniques.

This project proposes an **Actor-Critic (AC)** algorithm with **Boltzmann exploration**, implemented within a SUMO-based traffic simulation environment. The learning agent, instantiated per intersection, utilizes a value-function-based architecture with continuous policy updates and temperature-based action selection, aiming to balance exploration and exploitation during learning.

---

## 2. Method

The framework integrates **Actor-Critic Reinforcement Learning** with **Boltzmann exploration** for adaptive traffic signal control across intersections.

### Components

- **State Space**  
  The agent’s observation includes current traffic light phase, queue length, lane occupancy, vehicle speed, and other sensor-derived values:
  
  $$
  s_t = \{ \text{phase}_t, \text{queue\_length}_t, \text{occupancy}_t, \text{speed}_t, \dots \}
  $$

- **Action Space**  
  The agent can choose from predefined signal control strategies such as switching phases or extending green durations:

  $$
  a_t \in \{ \text{keep\_phase}, \text{switch\_phase}, \dots \}
  $$

- **Boltzmann Exploration**  
  Actions are selected according to the softmax distribution over Q-values:

  $$
  P(a|s) = \frac{e^{Q(s,a)/\tau}}{\sum_{a'} e^{Q(s,a')/\tau}}
  $$

  where \( \tau \) is the temperature parameter controlling the randomness of selection.

- **Critic Update**  
  The value function is updated using the temporal difference (TD) error:

  $$
  \delta_t = r_t + \gamma V(s_{t+1}) - V(s_t)
  $$

- **Actor Update**  
  The policy is improved using gradients weighted by the TD error.

---

## 3. Experiments

### Simulation Setup

The training is executed in the **SUMO** environment using multiple intersections with E2/E3 detectors for state collection and reward feedback.

### Performance Metrics

- **Queue Length**: Number of vehicles waiting per lane.
- **Average Speed**: Mean speed of vehicles approaching the intersection.
- **Waiting Time**: Cumulative stop time at traffic lights.
- **Lane Occupancy**: Ratio of occupied length to lane length.
- **Pressure**: Difference in vehicle counts between incoming and outgoing lanes.

### Reward Function

The reward is designed to **minimize waiting time**, computed as:

$$
\mathcal{R}_t = D_t - D_{t+1}
$$

Where \( D_t \) and \( D_{t+1} \) are total waiting times at consecutive time steps. A positive reward is given when the system reduces vehicle delay.

---

## 4. Conclusions

This project validates the potential of the **Actor-Critic with Boltzmann exploration** algorithm in controlling multiple intersections. Key findings include:

- Improved traffic efficiency and reduced congestion.
- Enhanced adaptability to real-time traffic conditions.
- Stability in learning due to Boltzmann-based action sampling.

The modular design allows for easy expansion to other RL models (e.g., DQN, DDPG) and supports flexible reward and state configurations for real-world deployment.

---

## 🛠️ Code Structure

```bash
├── main_AC_boltzmann.py           # Main training loop
├── init/
│   └── init_AC.py                 # Parameter and YAML loading
├── agent_ex/
│   └── intersection_agent.py      # Agent class with Q-table and Boltzmann logic
├── traci_ex/
│   ├── basic_commands.py          # SUMO simulation step commands
│   ├── value_changing.py          # Signal switching
│   └── value_retrieval.py         # State extraction functions
├── save_data/
│   └── save_data.py               # Reward and episode data saving
├── yaml_config/
│   └── _config_Actor-Critic(AC).yaml  # Configurations (not included)
