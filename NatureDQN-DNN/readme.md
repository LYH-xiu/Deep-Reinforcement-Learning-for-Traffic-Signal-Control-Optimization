# NatureDQN-DNN

---

### Table of contents 
1. [Introduction](#1-introduction)
2. [Method](#2-method)
3. [Experiments](#3-experiments)
4. [Results](#4-results)
5. [Conclusions](#5-conclusions)

---
### 1. Introduction 

- NatureDQN-DNN is a deep reinforcement learning algorithm that combines Q-learning with deep neural networks (DNNs) to approximate the Q-value function for high-dimensional inputs, such as images. It employs experience replay to stabilize training by reducing correlations between samples and uses a target network to provide stable Q-value targets. The DNN architecture typically includes convolutional layers for feature extraction and fully connected layers for decision-making. By addressing instability in Q-learning, NatureDQN-DNN achieves human-level performance in complex tasks like Atari games. This approach bridges deep learning and reinforcement learning, paving the way for advanced RL algorithms. Its success highlights the potential of deep neural networks in solving high-dimensional control problems.

### 2. Method 

- #### Q-Learning Update Rule

This formula updates the Q-value for a state-action pair using the Bellman equation, combining the immediate reward and the discounted future reward estimated by the target network.

$$
Q(s, a) \leftarrow r + \gamma \cdot \max_{a'} Q'(s', a')
$$

Here, $$Q(s, a)$$ represents the Q-value function measuring the expected cumulative reward for taking action $$a$$ in state $$s$$. $$r$$ denotes the immediate reward received after taking action $$a$$ in state $$s$$. $$\gamma$$ is the discount factor controlling the importance of future rewards ($$0 \leq \gamma \leq 1$$). $$Q'(s', a')$$ is the target Q-value for the next state $$s'$$ and action $$a'$$, estimated by the target network.

- #### Epsilon-Greedy Action Selection

This formula balances exploration and exploitation by selecting actions either randomly (with probability ϵ) or based on the highest Q-value (with probability 1−ϵ).

$$
a =\begin{cases}\text{random action}, & \text{with probability } \epsilon \\\arg\max_a Q(s, a), & \text{with probability } 1 - \epsilon
$$

Here, $$a$$ represents the selected action. $$\epsilon$$ denotes the exploration rate, which controls the probability of selecting a random action ($$0 \leq \epsilon \leq 1$$). $$Q(s, a)$$ is the Q-value function that measures the expected cumulative reward for taking action $$a$$ in state $$s$$.


- #### Target Network Update

This formula periodically updates the weights of the target network to match those of the main Q-network, ensuring stable training by providing consistent Q-value targets.

$$
\theta' \leftarrow \theta
$$

Here, $$\theta$$ represents the weights of the main Q-network. $$\theta'$$ denotes the weights of the target Q-network, which are periodically updated to match the main network's weights.

- #### Loss Function

This formula defines the loss function used to train the neural network, minimizing the difference between the predicted Q-values and the target Q-values computed using the Bellman equation.

$$
L(\theta) = \mathbb{E} \left[ \left( r + \gamma \cdot \max_{a'} Q'(s', a'; \theta') - Q(s, a; \theta) \right)^2 \right]
$$

Here, $$L(\theta)$$ represents the loss function used to optimize the neural network. $$r$$ denotes the immediate reward received after taking an action. $$\gamma$$ is the discount factor ($$0 \leq \gamma \leq 1$$). $$Q'(s', a'; \theta')$$ is the target Q-value predicted by the target network. $$Q(s, a; \theta)$$ is the Q-value predicted by the main network.


- #### Epsilon Decay

This formula reduces the exploration rate ϵ over time, gradually shifting the algorithm's focus from exploration to exploitation as training progresses.

$$
\epsilon \leftarrow \epsilon \cdot \epsilon_{\text{decay}}
$$

Here, $$\epsilon$$ represents the exploration rate, which decreases over time. $$\epsilon_{\text{decay}}$$ denotes the decay factor, controlling how quickly $$\epsilon$$ decreases ($$0 < \epsilon_{\text{decay}} < 1$$).

- #### Neural Network Architecture

This formula describes the forward propagation process in a multi-layer neural network, transforming input states into Q-values through hidden layers with activation functions.

$$h_1 &= f(W_1 \cdot x + b_1) \\h_2 &= f(W_2 \cdot h_1 + b_2) \\Q(s, a) &= W_3 \cdot h_2 + b_3
$$

Here, $$h_1$$ and $$h_2$$ represent the outputs of the hidden layers. $$f$$ denotes the activation function (e.g., ReLU or sigmoid). $$W_1$$, $$W_2$$, and $$W_3$$ are the weight matrices for each layer. $$b_1$$, $$b_2$$, and $$b_3$$ denote the bias terms for each layer. $$x$$ is the input vector (state representation). $$Q(s, a)$$ is the output Q-values for each action.

- #### Experience Replay

This formula represents the mechanism of sampling mini-batches of past experiences from a replay buffer, breaking correlations between consecutive samples to stabilize training.

$$
\text{Mini-batch} = \{(s_i, a_i, r_i, s'_i, \text{done}_i)\}_{i=1}^N
$$

Here, $$s_i$$ represents the state at time step $$i$$. $$a_i$$ denotes the action taken at time step $$i$$. $$r_i$$ is the reward received after taking action $$a_i$$ in state $$s_i$$. $$s'_i$$ represents the next state after taking action $$a_i$$. $$\text{done}_i$$ is a flag indicating whether the episode ended at time step $$i$$. $$N$$ denotes the batch size, representing the number of samples drawn from the replay buffer.


### 5. Conclusions 
When applying A3C to traffic signal control within SUMO, different agents can be set for various intersections. These agents use SUMO's API to obtain real-time traffic data (such as vehicle waiting times, queue lengths, etc.) as input states. Based on this information, agents decide when to switch the traffic light colors aiming to minimize total waiting time and optimize traffic flow. SUMO allows simulating complex traffic scenarios, enabling the A3C model to train and test under conditions close to the real world.

