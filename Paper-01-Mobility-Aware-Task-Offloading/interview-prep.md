# Interview Preparation — Paper 01

The goal is to explain the paper accurately and naturally, not to memorize a script.

## 1. Thirty-second explanation

> This paper studies computation offloading for delay-sensitive tasks in highly mobile Internet of Vehicles scenarios. Vehicles may move out of a ground edge server's coverage, which can increase delay or interrupt offloading. The authors propose ECMHI, a two-stage approach: a Transformer predicts future vehicle positions from historical position, speed, and direction data, and a MAPPO-based multi-agent reinforcement-learning method uses the predicted mobility information to make offloading decisions across local vehicles, edge servers, and UAVs. The objective is to reduce delay and energy consumption while meeting computing, timing, and UAV-energy constraints.

## 2. Ninety-second explanation

> The main challenge is that vehicles are moving while their computational tasks need to be completed within strict time limits. A fixed edge server may no longer be a suitable destination by the time the vehicle's task is processed. The paper addresses this with a three-tier architecture involving local vehicle computing, ground edge servers, and UAVs that can act as mobile auxiliary edge nodes.
>
> The method has two stages. First, a Transformer receives historical vehicle position, speed, and direction information and predicts future positions. The system uses those predictions to estimate whether a vehicle will remain within the edge server's coverage and whether a UAV should be considered. Second, MAPPO models vehicles as agents that choose where to execute or offload tasks. The reward penalizes total delay and energy consumption, while the model enforces resource, timing, and UAV-energy constraints.
>
> For evaluation, the prediction stage uses NGSIM data and compares Transformer with LSTM, GRU, and RNN. The offloading stage uses a synthetic vehicular 5G dataset and compares ECMHI with MAPPO, COMA, and MATD3. The authors report favourable prediction and offloading performance for their proposed method in the tested settings.

## 3. Likely technical questions

### Q1. What problem does the paper solve?

It addresses task offloading for delay-sensitive applications in high-mobility IoV environments, where vehicles may move out of an edge server's coverage while tasks are being transferred or processed.

### Q2. Why is computation offloading needed?

Some in-vehicle tasks require more computing capability or faster processing than a vehicle can provide locally. Offloading allows tasks to use nearby edge or UAV computing resources.

### Q3. Why are conventional fixed edge servers not always sufficient?

Their computing resources and coverage are limited. Vehicle movement can cause handovers and communication changes, increasing delay or making an offloading path unsuitable.

### Q4. What role does the UAV play?

It acts as an auxiliary mobile edge-computing node. It can move toward a vehicle expected to leave the ground edge server's coverage, subject to travel-time, computing-resource, and energy constraints.

### Q5. What are the two stages of ECMHI?

Stage 1 uses a Transformer to predict future vehicle positions. Stage 2 uses MAPPO to optimize computation-offloading decisions using predicted mobility information.

### Q6. Why does the paper use a Transformer?

The paper uses self-attention and positional encoding to model relationships in historical movement sequences and predict future vehicle positions. It compares the Transformer with LSTM, GRU, and RNN.

### Q7. What are the Transformer inputs and outputs?

The input sequence contains position, speed, and direction over time. The output is a sequence of future vehicle positions over a prediction horizon.

### Q8. What is MAPPO doing here?

MAPPO is used for the multi-agent task-offloading decision. Vehicle agents use their observations to select actions, while the Critic uses global state information to evaluate system value.

### Q9. What is the difference between state and observation in this paper?

The global state contains information about all vehicles, tasks, edge servers, and UAVs. An individual agent's observation contains local information available to that vehicle, such as its own task and vehicle characteristics.

### Q10. What are the possible offloading destinations?

The vehicle itself, a ground edge server, or a UAV.

### Q11. What does the reward function optimize?

It gives a negative weighted sum of total delay and total energy consumption. The weights \(\alpha\) and \(\beta\) satisfy \(\alpha+\beta=1\).

### Q12. Why are both delay and energy important?

A low-delay decision may consume more communication, computation, or UAV-movement energy. The method tries to balance the two objectives while respecting constraints.

### Q13. Which datasets are used?

NGSIM vehicle trajectory data is used for mobility prediction. A synthetic vehicular 5G dataset is used for the computation-offloading stage.

### Q14. Which baselines are used?

For prediction: LSTM, GRU, and RNN. For offloading: MAPPO, COMA, and MATD3.

### Q15. What communication assumption should you remember?

The paper uses a large-scale path-loss/geometric-distance model and omits small-scale fading. The authors justify this for time-scale separation, system-level focus, and computational tractability.

### Q16. What is one limitation or scope boundary?

The offloading stage uses a synthetic vehicular 5G dataset, and the paper reports computational experiments rather than a physical UAV field deployment. Its conclusions should be interpreted within that evaluation setup.

### Q17. What future work do the authors mention?

They identify Age of Information and security-aware service offloading in MEC-enabled 6G networks.

### Q18. What is the most important idea you learned?

A mobility prediction model can improve a downstream network decision when its predictions are integrated into the offloading strategy. The paper connects future vehicle position estimation with proactive UAV-assisted edge computing.

## 4. A simple whiteboard explanation

Draw these elements from left to right:

1. A vehicle travelling toward the boundary of a roadside edge server.
2. A Transformer receiving the vehicle's historical position, speed, and direction.
3. The predicted future vehicle position beyond the edge-server coverage.
4. A UAV moving toward the predicted position.
5. A MAPPO decision block selecting local, edge, or UAV execution.
6. Two evaluation outputs: total delay and total energy.

Then explain that the Transformer predicts **where the vehicle will be**, while MAPPO helps decide **where the task should execute**.

## 5. How to describe your work honestly

For this repository, a safe description is:

> I prepared a structured technical review of this published paper. My analysis covers the research problem, system architecture, Transformer-based mobility prediction, MAPPO-based task offloading, experimental design, assumptions, and potential research questions.

Do not say that you developed ECMHI, trained the authors' models, reproduced the experiments, or proposed the published method unless you actually complete that work independently.

## 6. Self-assessment checklist

Before adding this paper to your interview discussion list, make sure you can:

- [ ] Explain the problem without reading the abstract.
- [ ] Draw and explain the two-stage architecture.
- [ ] Distinguish the Transformer stage from the MAPPO stage.
- [ ] Explain the MDP state, observation, action, and reward.
- [ ] Explain why delay and energy are both optimized.
- [ ] Name the datasets and comparison algorithms.
- [ ] State at least two assumptions or limitations.
- [ ] Identify one possible extension and explain why it might help.
