# DDQN-DNN for Multi-Intersection Traffic Signal Control

## Table of contents

1. [Introduction](#1-introduction)  
2. [Method](#2-method)  
3. [Experiments](#3-experiments)  
4. [Conclusions](#4-conclusions)

---

## 1. Introduction

With the evolution of intelligent traffic management and reinforcement learning techniques, optimizing signal control across multiple intersections has become a critical research direction. Traditional fixed-time strategies often fail to adapt to real-time traffic variations, leading to congestion and inefficiency.

To tackle these issues, we propose a **Deep Double Q-Network with Deep Neural Network (DDQN-DNN)** approach, enabling agents to learn adaptive control policies directly from simulation feedback. The model leverages powerful function approximation capabilities of DNN and the stability improvements of the Double DQN algorithm.

This framework trains one agent per intersection using a DDQN backbone with a configurable neural network, allowing for flexible policy learning and improved decision-making under dynamic traffic scenarios.

---

## 2. Method

The **DDQN-DNN** algorithm is implemented based on the following components:

- **State Space**: Each intersection agent observes traffic features such as:
  - Lane occupancy, vehicle count, average speed, and congestion length
  - Data is collected using E2/E3 detectors

- **Action Space**: Discrete phase-switching decisions:
  $$
  a_t \in \{ \text{keep\_current}, \text{switch\_to\_phase}_1, \text{switch\_to\_phase}_2, \dots \}
  $$

- **Reward Function**: Designed to **minimize traffic delay**:
  $$
  \mathcal{R}_t = \frac{1}{\text{MeanTimeLoss}_t + \varepsilon}
  $$

- **Q-value Function**: The target value is computed via Double DQN:
  $$
  y = r + \gamma Q_{\text{target}}(s', \arg\max_{a} Q_{\text{online}}(s', a; \theta); \theta^-)
  $$
  where:
  - \( Q_{\text{online}} \): online Q-network
  - \( Q_{\text{target}} \): target Q-network
  - \( \theta, \theta^- \): network parameters

### Experience Replay

To improve learning efficiency and stability, the algorithm uses experience replay and periodic target updates.

---

## 3. Experiments

### Performance Evaluation Metrics

To evaluate the performance of the **DDQN-DNN** model, the following indicators are recorded:

- **Queue Length**: Number of vehicles waiting at each intersection.
- **Waiting Time**: Total stop duration at red lights.
- **Average Speed**: Mean speed of vehicles approaching the intersection.
- **Lane Occupancy**: Ratio of occupied length per lane.
- **Pressure**: Difference in vehicle count between incoming and outgoing lanes.

### Reward Design

The reward is defined inversely to the average **time loss** on the E3 detectors:

$$
\mathcal{R}_t = \frac{1}{\text{TimeLoss}_t}
$$

This encourages the agent to reduce cumulative delays over time.

---

## 4. Conclusions

The proposed **DDQN-DNN** framework demonstrates effective traffic signal control under various traffic scenarios. Compared to classical Q-learning, the **DDQN-DNN** model:

- Achieves lower queue lengths and waiting times
- Maintains smoother vehicle flow (higher average speeds)
- Adapts well to dynamic traffic conditions

Simulations are conducted in **SUMO**, where agents are deployed at different intersections and learn independently. Configurations are defined through YAML files for reproducibility and modularity.

---

## Code Structure

```bash
├── main_DDQN_DNN.py               # Main training script
├── agent_ex/
│   └── ddqn_dnn_tls_agent.py      # Agent logic with experience replay and DDQN
├── init/
│   ├── import_modules.py
│   └── init_dnn.py                # Loads YAML parameters
├── utils/
│   └── process_control_ddqn_dnn.py
├── traci_ex/
│   ├── value_retrieval.py         # From E2/E3 detectors
│   ├── value_changing.py          # Traffic light manipulation
│   └── basic_commands.py          # SUMO simulation step logic
├── save_data/
│   └── save_data.py               # Save reward/loss per episode
