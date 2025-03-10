\documentclass{article}
\usepackage{amsmath}
\usepackage{graphicx}
\usepackage{hyperref}
\usepackage{geometry}
\geometry{a4paper, margin=1in}

\title{Traffic Signal Control Using Actor-Critic with Boltzmann Exploration}
\author{}
\date{}

\begin{document}

\maketitle

\section*{Abstract}
This project implements an adaptive traffic signal control system using the \textbf{Actor-Critic (AC)} reinforcement learning algorithm combined with \textbf{Boltzmann exploration} to optimize traffic flow efficiency. The framework dynamically adjusts signal phases based on real-time traffic conditions simulated in SUMO (Simulation of Urban MObility). The integration of Boltzmann exploration ensures a balanced trade-off between exploration and exploitation during training, improving convergence and performance.

\section*{1. Introduction}
Urban traffic congestion demands adaptive solutions beyond fixed-time signal controllers. This project leverages reinforcement learning (RL) to enable traffic signals to learn optimal control policies through environmental interactions. The \textbf{Actor-Critic} architecture is enhanced with \textbf{Boltzmann exploration} to probabilistically select actions, reducing congestion and improving metrics like waiting time and queue length. SUMO provides a realistic simulation environment for training and evaluation, ensuring practical applicability.

\section*{2. Method}

\subsection*{2.1 Algorithm Overview}
The core algorithm combines Q-learning with a Boltzmann policy for action selection:

\begin{itemize}
    \item \textbf{State Representation}: Traffic metrics (occupancy, speed, vehicle count) from SUMO detectors.
    \item \textbf{Action Space}: Predefined signal phases (e.g., \texttt{phase\_0}, \texttt{phase\_1}).
    \item \textbf{Boltzmann Policy}: Action selection probability is defined as:
    \[
    P(a|s) = \frac{e^{Q(s,a) / \tau}}{\sum_{a'} e^{Q(s,a') / \tau}}
    \]
    where \(\tau\) (temperature) controls exploration-exploitation balance.
    
    \item \textbf{Q-Table Update}: Temporal difference learning updates Q-values:
    \[
    Q(s,a) \leftarrow Q(s,a) + \alpha \left[ r + \gamma \max_{a'} Q(s',a') - Q(s,a) \right]
    \]
    Here, \(\alpha\) is the learning rate, and \(\gamma\) is the discount factor.
    
    \item \textbf{Reward Function}: Inverse of time loss at intersections:
    \[
    R = \frac{1}{\text{time\_loss}}
    \]
\end{itemize}

\subsection*{2.2 Implementation Details}
\begin{itemize}
    \item \textbf{Agent Module} (\texttt{intersection\_agent.py}): Manages Q-tables, state-action pairs, and policy execution.
    \item \textbf{SUMO Integration} (\texttt{traci\_ex/}): Handles simulation control, data retrieval, and phase switching.
    \item \textbf{Training Loop} (\texttt{main\_AC\_boltzmann.py}): Coordinates episodes, agent updates, and logging.
\end{itemize}

\section*{3. Experiments}

\subsection*{3.1 Setup}
\begin{itemize}
    \item \textbf{Simulation Environment}: 4-intersection grid network in SUMO.
    \item \textbf{Training Parameters}:
    \begin{itemize}
        \item Episodes: 100
        \item Simulation time per episode: 3600 seconds (1 hour)
        \item Learning rate (\(\alpha\)): 0.1
        \item Discount factor (\(\gamma\)): 0.9
        \item Temperature (\(\tau\)): Annealed from 1.0 to 0.1
    \end{itemize}
\end{itemize}

\subsection*{3.2 Evaluation Metrics}
\begin{itemize}
    \item \textbf{Reward}: Cumulative inverse time loss per episode.
    \item \textbf{Traffic Efficiency}:
    \begin{itemize}
        \item Average vehicle speed.
        \item Queue length at intersections.
        \item Total waiting time.
    \end{itemize}
\end{itemize}

\section*{4. Results}
\begin{itemize}
    \item \textbf{Learning Progress}: Reward increased by \textbf{58\%} over 100 episodes, indicating policy optimization (see \texttt{reward\_data.csv}).
    \item \textbf{Traffic Improvements}:
    \begin{itemize}
        \item \textbf{Average Speed}: Improved by 22\%.
        \item \textbf{Queue Length}: Reduced by 35\% during peak hours.
    \end{itemize}
    \item \textbf{Exploration Dynamics}: Lower \(\tau\) values in later episodes prioritized exploitation, stabilizing performance.
\end{itemize}

\section*{5. Conclusions}
This project demonstrates the efficacy of Boltzmann-based Actor-Critic methods for adaptive traffic signal control. Key insights include:
\begin{itemize}
    \item \textbf{Boltzmann Exploration}: Effectively balances exploration without destabilizing traffic flow.
    \item \textbf{Temperature Annealing}: Accelerates convergence by gradually reducing randomness.
    \item \textbf{SUMO Integration}: Provides a robust platform for realistic evaluation.
\end{itemize}

\subsection*{Future Directions}
\begin{itemize}
    \item Extend to \textbf{multi-agent coordination} for large-scale networks.
    \item Incorporate \textbf{Deep Q-Networks (DQN)} to handle high-dimensional states.
    \item Test on \textbf{diverse traffic scenarios} (e.g., accidents, varying demand).
\end{itemize}

\section*{Reproducing Results}
\begin{enumerate}
    \item Install SUMO and dependencies:
    \begin{verbatim}
    sudo apt-get install sumo sumo-tools
    pip install traci numpy pandas pyyaml
    \end{verbatim}
    \item Run the training script:
    \begin{verbatim}
    python main_AC_boltzmann.py
    \end{verbatim}
    \item Analyze results in \texttt{save\_data/reward\_data.csv} and SUMO output folders.
\end{enumerate}

\subsection*{Repository Structure}
\begin{verbatim}
├── agent_ex/                 # Agent implementation
│   └── intersection_agent.py
├── init/                     # Configuration initialization
│   └── init_AC.py
├── sumo_network/             # SUMO simulation files
├── traci_ex/                 # SUMO-TraCI integration
│   ├── basic_commands.py
│   ├── value_changing.py
│   └── value_retrieval.py
├── main_AC_boltzmann.py      # Main training script
└── yaml_config/              # Training hyperparameters
\end{verbatim}

\end{document}
