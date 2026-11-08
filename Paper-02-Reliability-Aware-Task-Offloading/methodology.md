# Methodology — Paper 02

## 1. System architecture

The paper considers a UAV-assisted MEC network with:

- `N` UAVs that offer computing and wireless access.
- `M` user equipment (UE) devices that generate computational tasks.
- One edge-cloud (EC) server.

Each UE can process a task locally, offload it to any one of the UAVs, or offload it to the EC server. UAVs can serve multiple UEs and keep FIFO execution queues. The EC has abundant computing resources in the model and does not maintain the same type of computation queue described for UEs/UAVs.

The architecture is not fully decentralized. The edge-cloud server coordinates task-offloading and UAV-trajectory decisions using distributed information. The authors motivate this choice by the limited computing and energy resources available on individual UAVs and by the value of decisions using system-wide information.

## 2. Why the paper is task-driven

The system advances according to the arrival of computational tasks. When task `k` arrives at time `t_k`, the controller makes an offloading decision for that task rather than waiting for the end of a fixed time slot.

The authors give two reasons:

1. The task can be considered immediately, avoiding waiting for a time-slot boundary.
2. The controller makes a decision for one task at a time rather than deciding the offloading status of all tasks at every slot, reducing the decision dimension and implementation complexity.

The paper describes this approach as especially relevant when task-generation frequency is low. Its suitability under very high or bursty arrival rates remains an important question for broader deployment.

## 3. Task representation and possible destinations

Task `k` is represented by:

`(c_k, u_k, t_k, m_k, d_k)`

where `c_k` is the computing workload, `u_k` is the data transmission size, `t_k` is the continuous-valued arrival time, `m_k` is the generating UE, and `d_k` is the maximum permissible delay.

The task is assigned to exactly one destination:

| Destination variable | Execution location |
|---|---|
| `i_k = 0` | Local processing on UE `m_k` |
| `i_k ∈ {1, ..., N}` | Processing on UAV `i_k` |
| `i_k = N + 1` | Processing on the edge-cloud server |

This is whole-task (binary) offloading: the task is not partitioned among several destinations.

## 4. UAV mobility and constraints

Each UAV has a 3D location:

`w_n(k) = [x_n(k), y_n(k), z_n(k)]ᵀ`

The horizontal and vertical displacement between consecutive task arrivals is limited by the UAV's maximum horizontal and vertical speeds and by the elapsed time between arrivals.

The authors additionally impose a minimum separation `D_min` between every pair of UAVs to avoid collisions. UAV flight energy is modeled as a function of flight speed and the interval between task arrivals. As a result, trajectory decisions affect both communication quality and the energy budget.

## 5. Reliability model

The reliability model has two components.

### 5.1 Computing-unit reliability

For computing node `j`, failures during a task's computation interval are modeled as a Poisson process with rate `alpha_j`. If `t^co_{j,k}` is the time required to execute task `k` at node `j`, the probability of zero failures during that interval is:

`R^c_{j,k} = exp(-alpha_j * t^co_{j,k})`

The paper mainly discusses soft errors (e.g., unexpected restart/shutdown, software bugs, outages, and communication errors). A hardware-failed device can be represented by setting its failure-rate parameter very high so that its reliability approaches zero and it is effectively not assigned tasks. The authors assume a one-off fault that does not affect later tasks after recovery.

### 5.2 Transmission-link reliability

For a link from UE `m` to destination `j`, the no-failure probability is modeled as:

`R^t_{m,j,k} = exp(-beta_{m,j} * t^tr_{m,j,k})`

where `beta_{m,j}` is the link failure rate and `t^tr` is transmission time.

### 5.3 End-to-end reliability

For an offloaded task, the paper multiplies the reliability of computation at the selected destination by the reliability of the transmission link. For local execution, only the local computing reliability is involved. For cloud offloading, the same structure is applied to the UE-to-EC transmission and cloud computation.

This is distinct from the overall task-success-rate metric, because a task must also finish before its deadline.

## 6. Communication and computing models

For UE-to-UAV communication, the authors use a probabilistic LoS/NLoS path-loss model. The LoS probability is a function of the UAV's elevation angle relative to the UE; the average path loss combines LoS and NLoS path losses weighted by their probabilities. This is intended to reflect obstacles and scatterers more realistically than a free-space-only assumption.

The transmission rate follows a Shannon-style expression based on bandwidth, transmit power, channel gain, and noise power. Transmission time is the task's data size divided by the corresponding transmission rate.

The local UE and UAVs maintain computation queues. UAV queues are FIFO. The waiting/computation delay depends on the backlog at the time a task arrives and the computing capability of the device. The EC is modeled as having abundant resources and no computation queue, but its greater distance can increase transmission delay.

## 7. Completion time and task success

For a task, completion time depends on the selected destination:

- **Local UE:** local queueing and computation delay.
- **UAV:** UE-to-UAV transmission delay plus UAV queueing/computation delay.
- **EC:** UE-to-EC transmission delay plus cloud computation delay.

The completion-time condition is:

`T(k) <= d_k`

If the deadline is missed, the task is overdue and dropped. A task is counted as successful only if it completes before the deadline and the required execution/transmission does not fail.

The cumulative success rate is defined using counts of successful, dropped, and failed tasks:

`ST(k) = |K_succ(k)| / (|K_succ(k)| + |K_drop(k)| + |K_fail(k)|)`

## 8. Joint optimization problem

The paper jointly optimizes three groups of variables:

1. `i_k`: task offloading destination.
2. `w(k)`: 3D positions of all UAVs.
3. UE transmit power for the task.

The objective is to maximize the long-term average task-success rate. The constraints include:

- UE transmit power cannot exceed its maximum.
- The destination must be one of the allowed local/UAV/EC choices.
- UAV horizontal and vertical movement must obey speed limits.
- UAVs must maintain the minimum collision-avoidance separation.
- UAV flight/computation energy and UE local/transmission energy must remain within their budgets.

## 9. MDP formulation

The authors formulate the optimization problem as a Markov Decision Process because the next system state depends on the current state and the action. The sequence is indexed by task arrivals.

### State

The state includes the current UE workload/waiting delay, the generating UE's computing capability, the workload/waiting delays of all UAVs, transmission delays to UAVs and the EC, UAV computing capabilities, task workload, and task deadline. The paper gives the state-space dimension as `3N + 6`.

### Action

The action jointly contains:

- A discrete destination `i_k`.
- Continuous 3D positions for the `N` UAVs (`3N` coordinate values).
- A continuous UE transmission-power variable.

The paper gives the full action dimension as `3N + 2` and identifies the space as discrete-continuous hybrid.

### Reward

The reward is shared by the nodes as a team reward. A successfully processed task yields `+1`; a dropped or failed task yields `-1`. The system-level reward is the sum of feedback across nodes.

## 10. Dependence-aware latent action representation

The discrete destination and continuous control variables are coupled. The authors propose a learned representation rather than simply rounding continuous values or ignoring one class of variables.

1. **Discrete embedding table:** A trainable table has `N + 2` embeddings, one for each possible offloading destination.
2. **Conditional encoder:** A conditional VAE encoder receives the continuous action, system state, and selected discrete-action embedding. It produces a Gaussian latent variable `z`.
3. **Conditional decoder:** It reconstructs the continuous action from `z`, state, and discrete embedding.
4. **Discrete decoding:** A nearest-neighbour lookup in the embedding table maps the learned discrete latent vector back to a valid destination.

The final latent action is represented by two vectors `(g, z)`, one associated with the discrete action embedding and one associated with the continuous action representation.

The representation is trained with a VAE reconstruction/KL objective and a prediction loss on state-transition residuals. This gives the representation an incentive to preserve action information relevant to environment dynamics.

## 11. TD3 integration and TRO training

The actor network maps the current system state to a latent vector `(g, z)`. The decoder maps that latent output to the original hybrid action. Two critic networks estimate the value of the decoded action. A replay buffer stores transitions and mini-batches are sampled to update the critics and actor.

Training has two broad phases:

- **Warm-up:** Pre-train the embedding table and encoder/decoder representation using experience samples from the replay buffer.
- **Learning:** The actor generates latent actions, the decoder creates the hybrid decision, the environment returns a reward and next state, and the transition is stored. Critics and actor are updated; representation components continue to be trained during the learning process.

This combined algorithm is named **Task-driven Reliability-aware Offloading (TRO)**.

## 12. Evaluation design

The authors use two evaluation modes:

1. **Simulation:** Python 3.9 and PyTorch 2.2, with the task-success rate evaluated against four alternatives and under changing task/network conditions.
2. **Kubernetes-based testbed:** A TCP/IP-connected setup with model-training, control, and execution layers, used to check practicality beyond a purely simulated node-to-node communication model.

The detailed configuration and reported trends are documented in [`experiments-and-results.md`](experiments-and-results.md).
