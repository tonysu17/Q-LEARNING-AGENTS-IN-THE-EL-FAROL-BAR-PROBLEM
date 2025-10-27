# Q-Learning Agents in the El Farol Bar Problem

This project is an agent-based simulation of the El Farol Bar Problem (EFBP) using tabular Q-learning agents. It investigates how boundedly rational agents, using reinforcement learning, can learn to coordinate their attendance at a resource-constrained venue.

The simulation explores the emergent dynamics of the system, focusing on attendance volatility, efficiency, and the equity of outcomes (payoff distribution) among agents.

## Table of Contents
- [About the Project](#about-the-project)
- [Methodology](#methodology)
  - [Agent Design](#agent-design)
  - [State Representation](#state-representation)
  - [Learning and Action](#learning-and-action)
- [Key Findings](#key-findings)
- [Running the Simulation](#running-the-simulation)
  - [Installation](#installation)
  - [Usage](#usage)
  - [Configuration](#configuration)
- [Citation](#citation)


## About the Project

The El Farol Bar Problem, first introduced by W. Brian Arthur (1994), is a canonical model for studying coordination in multi-agent systems. The problem is as follows:

* There are $N$ agents (e.g., 100).
* There is a bar with a capacity threshold $C$ (e.g., 60).
* Each week (or round), agents must independently decide whether to "Attend" the bar or "Stay Home".

**Payoff:**
* If an agent Attends and the bar is not crowded (attendance $\le C$), they get a high reward ($G$).
* If an agent Attends and the bar is crowded (attendance $> C$), they get a low reward ($B$).
* If an agent Stays Home, they get a medium, stable reward ($S$), where $G > S > B$.

The problem is challenging because if all agents predict the bar will be uncrowded, they will all go, making it crowded. This model serves as a representation of market dynamics where agents must make decisions based on anticipating the actions of others.

This project replaces the original heuristic-based agents with more sophisticated Q-learning agents to study their inductive learning and adaptation in this non-stationary environment.

## Methodology

### Agent Design
The system is modeled as an agent-based simulation. Each of the $N$ agents is an independent Q-learner. Agents are initialized with heterogeneous Q-tables (filled with uniformly random values) to ensure diversity.

### State Representation
A key feature of this model is the agent's "memory". The state $s$ is not the exact number of attendees, but rather a binary string of length $M$ representing the history of outcomes.

* Each bit in the string represents one past round.
* `1` might represent a "crowded" night.
* `0` might represent an "uncrowded" night.

For a memory $M=3$, a state like `s = (1, 0, 0)` could mean "It was crowded 3 weeks ago, but uncrowded the last 2 weeks".

This approach keeps the state space manageable, growing at $\mathcal{O}(2^M)$, and allows agents to detect and exploit temporal patterns.

### Learning and Action

**Action Selection:** Agents use a softmax (Boltzmann) policy to choose an action (Attend or Stay Home). The probability of choosing an action $a$ in state $s$ is proportional to its exponentiated Q-value:

$$
\pi_t(s,a) = \frac{\exp(Q_t(s,a) / \tau_t)}{\sum_{a'} \exp(Q_t(s,a') / \tau_t)}
$$

The temperature $\tau$ controls the exploration-exploitation trade-off. An $\epsilon$-greedy policy is also discussed as an alternative.

**Q-Value Update:** After all agents act and the outcome (crowded/uncrowded) is revealed, each agent receives a reward $r_t$ and observes the new state $s_{t+1}$. They then update their Q-table using the temporal difference update rule:

$$
Q_{t+1}(s_t, a_t) = Q_t(s_t, a_t) + \alpha [r_t + \gamma \max_{a'} Q_t(s_{t+1}, a') - Q_t(s_t, a_t)]
$$

* $\alpha$ is the learning rate.
* $\gamma$ is the discount factor.

## Key Findings

The simulation successfully reproduces the hallmark dynamics of the El Farol Bar Problem and provides new insights into the role of agent sophistication.
