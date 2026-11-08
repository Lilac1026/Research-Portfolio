# Concept Notes — Paper 02

These notes explain the ideas needed to understand *Reliability-Aware Optimization of Task Offloading for UAV-Assisted Edge Computing*. Definitions are simplified for revision; the paper's notation and modeling choices are retained where possible.

## 1. The system in simple terms

A user equipment (UE)—for example, a phone, sensor, or other connected device—generates a computational task. The UE has three choices:

1. **Local processing:** execute the task on the UE itself.
2. **UAV offloading:** transmit the task to one selected UAV that has computing resources.
3. **Edge-cloud offloading:** transmit the task to the edge-cloud (EC) server.

The system contains multiple UAVs, multiple UEs, and one edge-cloud server. UAVs have queues and finite computing/energy resources. The paper says the edge-cloud server coordinates offloading and UAV-trajectory decisions using distributed system information; the system is not fully decentralized.

## 2. Why UAV-assisted MEC?

**Multi-access Edge Computing (MEC)** brings computing closer to users so that tasks do not always need to travel to a remote cloud. This can help reduce communication delay. In rural areas, emergency-response settings, disaster zones, or crowded locations, fixed ground infrastructure may be missing, overloaded, or damaged.

A **UAV** can be moved to provide temporary communication and computing support. However, it has constraints of its own: battery/energy, flight speed, flight movement, processor capacity, and possible hardware or software failures.

## 3. Task offloading

Task offloading is the decision about **where a task should be executed**. In this paper, a task is offloaded as a whole; it is not split into smaller pieces across multiple destinations.

For task `k`, the paper represents the task as:

`(c_k, u_k, t_k, m_k, d_k)`

| Symbol | Meaning |
|---|---|
| `c_k` | Computing workload, measured in CPU cycles |
| `u_k` | Data size to transmit |
| `t_k` | Task arrival time (continuous-valued) |
| `m_k` | UE that generated the task |
| `d_k` | Permissible delay / task deadline threshold |

The destination variable `i_k` has `N + 2` possible choices when there are `N` UAVs:

- `i_k = 0`: execute locally on the generating UE.
- `i_k = 1, ..., N`: offload to the selected UAV.
- `i_k = N + 1`: offload to the edge-cloud server.

## 4. Time-driven versus task-driven decisions

### Time-driven

The system is divided into fixed time slots. An offloading decision is made at a slot boundary. If a task arrives just after a decision point, it may have to wait for the next one. Very short slots can also cause frequent decisions and additional computation overhead.

### Task-driven

A new task arrival triggers an offloading decision for that task. The model does not describe evolution by repeatedly advancing a fixed decision time slot. It follows the sequence of task arrivals.

**Why the authors use it:** It can avoid waiting for a time-slot boundary and considers one task's destination at a time rather than making a large joint decision for every active task at every slot. The authors argue this reduces decision waiting and implementation complexity.

**Important qualification:** The paper itself notes that the suitability of a task-driven scheme depends on the task-arrival pattern. Its discussion describes it as particularly suitable when task-generation frequency is low. Do not claim that task-driven control is universally better for every traffic pattern.

## 5. Reliability: two different failure sources

The paper models two aspects of system reliability:

### Computing-unit reliability

A UE or UAV may fail while executing a task. Examples discussed include unexpected restart/shutdown, software bugs, outages, communication-related errors, and hardware failures. The model treats failures during a processing interval as a Poisson process with rate `alpha_j`.

If the processing interval is `t_co`, the probability of zero failures—and therefore successful execution under this model—is:

`R_compute = exp(-alpha_j * t_co)`

A larger failure rate or longer processing time reduces the probability of no failure.

### Transmission-link reliability

A task may fail while being transmitted between a UE and a UAV or the edge cloud. The paper models the no-failure probability of a link using a link failure rate `beta` and transmission time `t_tr`:

`R_transmission = exp(-beta * t_tr)`

A longer transmission interval or less reliable link decreases the probability of successful transmission.

### Combined task reliability

For an offloaded task, the paper combines the computing and transmission reliability terms. For example, for a task sent to a UAV, the task's modeled reliability is the product of the UAV-computing reliability and the UE-to-UAV transmission reliability. The corresponding exponential factors multiply (equivalently, their exponents add).

**Do not confuse reliability with task success rate:** reliability models the probability of error-free execution/transmission, while the task-success metric also accounts for whether the task meets its deadline.

## 6. Deadline and task success

A task is successful only when it completes before its allowable delay and its execution/transmission is error-free. If its completion time exceeds the deadline, it is dropped from the edge network. A task can therefore fail in at least two ways:

- It can be **dropped/overdue** because it misses its deadline.
- It can **fail during execution or transmission** because of an unreliable computing unit or link.

The paper's cumulative task-success metric is:

`successful tasks / (successful tasks + dropped tasks + failed tasks)`

The optimization objective is to maximize the long-term average of this metric.

## 7. UAV and wireless-link modeling

### UAV movement

Each UAV has a 3D position `(x, y, z)`. Horizontal and vertical travel between consecutive task arrivals are bounded by maximum horizontal and vertical speeds. The model also enforces a minimum distance between UAVs to prevent collisions.

The paper relates flight power to UAV speed and models flight energy over the interval between task arrivals. Thus, placing UAVs near users is not free: movement consumes energy.

### LoS and NLoS communication

A **Line-of-Sight (LoS)** link has a direct path between UE and UAV. A **Non-Line-of-Sight (NLoS)** link may be obstructed by buildings or other objects. The paper uses a probabilistic LoS/NLoS path-loss model rather than relying only on a simple free-space path-loss model. The LoS probability depends on the UAV's elevation angle relative to the UE.

The average path loss is obtained by weighting the LoS and NLoS path-loss values by their corresponding probabilities. The inverse of average path loss is used as a channel-gain term in the transmission-rate model.

## 8. Queueing and service delay

The paper models computation queues at UEs and UAVs. UAV task queues follow First-In, First-Out (FIFO) order. A task may wait until earlier work is processed, even if its own computing time is short.

For an offloaded task, service delay includes:

- Uplink transmission delay from the UE to the selected UAV or edge cloud.
- Waiting/computation delay at the destination.

For local execution, the task is added to the UE's local computation queue; there is no wireless transmission delay.

The paper treats edge cloud as having abundant computing resources and no computation queue in its model, but cloud offloading can still incur longer wireless transmission delay because the cloud is farther away.

## 9. Hybrid action space

The decision is not purely discrete or purely continuous:

| Component | Type | Meaning |
|---|---|---|
| Offloading destination `i_k` | Discrete | Local UE, one of `N` UAVs, or edge cloud |
| UAV positions `w(k)` | Continuous | 3D coordinates of the UAVs |
| UE transmit power | Continuous | Power used for wireless offloading |

This is called a **discrete-continuous hybrid action space**. The discrete and continuous parts are dependent: the best transmit power and useful UAV position can depend on which destination is selected. Treating them as unrelated outputs may lose that relationship.

## 10. Dependence-aware latent representation

The authors represent a hybrid action in a learned continuous latent space:

1. A trainable **embedding table** maps each discrete offloading choice to a vector.
2. A **conditional VAE encoder** maps continuous action variables to a latent vector while conditioning on the current system state and the discrete-action embedding.
3. A **conditional decoder** reconstructs the continuous action from the latent representation and conditioning information.
4. A nearest-neighbour lookup in the embedding table maps the learned discrete embedding back to an actual discrete destination.

The resulting latent action has two parts, conventionally denoted `(g, z)`. This allows a continuous policy network to generate a representation that can be decoded into a discrete-continuous action.

## 11. TD3 and the TRO algorithm

**Twin Delayed Deep Deterministic Policy Gradient (TD3)** is a deep reinforcement-learning method designed for continuous control. The paper uses an actor network to produce latent actions and two critic networks to evaluate action values. The decoder converts the latent action into the actual hybrid decision.

The paper's combined method is called **Task-driven Reliability-aware Offloading (TRO)**. It has a representation-learning warm-up stage followed by a learning stage in which the actor, critics, decoder, and representation are trained using experience collected from the environment.

## 12. Terms to remember

| Term | Meaning in this paper |
|---|---|
| UE | User equipment that generates a task |
| UAV | Uncrewed aerial vehicle that can provide edge computing and communication support |
| EC | Edge-cloud server |
| MEC | Multi-access edge computing |
| FIFO | First-In, First-Out task queue order |
| LoS / NLoS | Line-of-Sight / Non-Line-of-Sight radio path |
| MDP | Markov decision process: state, action, reward, and state transition formulation |
| DRL | Deep reinforcement learning |
| VAE | Variational auto-encoder |
| TD3 | Twin Delayed Deep Deterministic Policy Gradient |
| TRO | Task-driven Reliability-aware Offloading |
| Task success rate | Successful tasks divided by successful + dropped + failed tasks |

## Quick self-check

Before moving to the technical deep dive, try answering these without looking:

1. Why can minimizing delay alone be an insufficient objective?
2. What triggers a decision in a task-driven system?
3. What are the `N + 2` offloading destinations?
4. What are the two reliability components modeled in the paper?
5. Why is the action space hybrid?
6. What does the embedding table do, and what does the conditional VAE do?
7. What is the role of TD3 in TRO?
