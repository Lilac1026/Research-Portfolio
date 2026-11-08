# Interview Preparation — Paper 02

Use these as answer frameworks, not scripts to memorize. Keep the explanation in your own words and be prepared to draw the system and algorithm flow.

## 1. 30-second paper explanation

> This paper studies reliable task offloading in a multi-UAV mobile edge-computing network. It argues that minimizing delay alone is not enough because a task can fail due to an unreliable UAV or wireless link. The authors propose a task-driven reliability-aware scheme called TRO, which makes decisions when tasks arrive and jointly optimizes the offloading destination, UAV positions, and UE transmission power. Since the action contains both discrete and continuous variables, they learn a dependence-aware latent representation and combine it with TD3 to maximize long-term task success rate.

## 2. A slightly deeper explanation

> The system has multiple UEs, multiple computing UAVs, and an edge-cloud server. A task can execute locally, on a selected UAV, or in the edge cloud. The model includes UE/UAV computation queues, deadlines, UAV movement and energy constraints, and separate reliability models for computing nodes and transmission links. The optimization is formulated as an MDP indexed by task arrivals. TRO uses a trainable embedding for the discrete destination and a conditional VAE for continuous controls, then a TD3 actor produces latent actions that are decoded into the original hybrid action. The authors evaluate it through simulations and a Kubernetes testbed with TCP/IP communication.

## 3. Likely technical questions

### Q1. What problem does the paper solve?

It solves cooperative task offloading in a multi-UAV-assisted MEC network where tasks can miss deadlines or fail during execution/transmission. It jointly considers offloading decisions, UAV positions, and UE transmission power, aiming to maximize long-term average task success rate.

### Q2. Why is minimizing delay alone insufficient?

A task may have a short nominal delay but still fail because a UAV or wireless link experiences a failure. Also, tasks mainly need to finish before their deadlines; reducing delay beyond what is required may not directly address reliability. The paper therefore makes task success rate the optimization objective.

### Q3. What is the difference between time-driven and task-driven offloading?

Time-driven methods make decisions at fixed time-slot boundaries. Task-driven offloading makes a decision when a new task arrives. The latter can avoid waiting for the next slot and makes a decision for one task at a time. Its suitability depends on task-arrival patterns; the paper especially motivates it for lower task-generation frequency.

### Q4. What are the possible task destinations?

For `N` UAVs, there are `N + 2` choices: local UE processing (`i_k = 0`), any one of the `N` UAVs, or the edge-cloud server (`i_k = N + 1`).

### Q5. What reliability components are modeled?

Computing-unit reliability and transmission-link reliability. Computing failures are modeled with a Poisson failure process, and link failures use a failure-rate model over the transmission interval. For offloaded tasks, the two reliability terms are combined.

### Q6. How is the probability of successful computation modeled?

If a computing node has failure rate `alpha_j` and task processing time `t_co`, the probability of zero failures during processing is `exp(-alpha_j * t_co)`.

### Q7. How is transmission reliability modeled?

For a link with failure rate `beta` and transmission time `t_tr`, the no-failure probability is `exp(-beta * t_tr)`.

### Q8. What makes the action space hybrid?

The destination selection is discrete, while UAV 3D positions and UE transmission power are continuous. These components are dependent, so the system must learn a joint representation.

### Q9. What does the dependence-aware latent representation do?

It maps discrete destinations to learned embeddings and maps continuous controls into a latent vector using a conditional VAE. The decoder reconstructs the continuous variables, while nearest-neighbour lookup recovers the discrete destination. This creates a latent representation that preserves relationships between action components.

### Q10. What is TD3 doing in the proposed scheme?

TD3 performs policy learning over the continuous latent action representation. The actor outputs latent actions; the decoder maps them to the original hybrid decision; twin critics evaluate the action; and replay-buffer samples support training.

### Q11. Why does the method use a VAE?

The conditional VAE provides a learned latent representation of continuous controls conditioned on the system state and discrete-action embedding. Its reconstruction and KL-divergence objectives help organize the continuous action space in a form that can be generated and decoded by the policy.

### Q12. What is the reward function?

A successful task returns `+1`. A task that is dropped or fails returns `-1`. The system uses a shared team reward across nodes to encourage cooperative behaviour.

### Q13. What are the main baselines?

TDR is time-driven and reliability-aware; NCR omits continuous-variable representation; NHR removes hybrid-action representation and uses rounding; NRA does not model reliability and optimizes average delay. TRO is compared against all four.

### Q14. What happens as task workload or transmission size increases?

The task-success rate decreases because greater computation workload increases processing time and greater transmission size increases transmission time and reduces modeled transmission reliability.

### Q15. What happens as the number of UAVs increases?

In the evaluated settings, adding UAVs increases available computing resources and improves task-success rate. The rate still remains below 1 because failures may occur.

### Q16. What did the Kubernetes testbed validate?

It added a three-layer Kubernetes-based setup in which nodes communicate over TCP/IP. The model-training layer contains the DRL model, replay buffer, and agent; the control layer configures services and generates tasks; and the execution layer deploys nodes and runs offloading decisions. It is more realistic than simulated inter-node communication, but is not a real UAV field trial.

### Q17. What are the limitations or future directions?

The authors identify fully decentralized systems and real-life experimental testing as future work. Critical areas for further study include variable/correlated failure rates, bursty task arrivals, field-calibrated wireless channels, and the dependence on a central edge-cloud controller.

## 4. Questions an interviewer might use to test depth

Be ready to explain:

- Why a Poisson failure model leads to an exponential no-failure probability.
- Why the task-success metric includes both dropped and failed tasks in the denominator.
- Why a UAV's 3D position affects both channel quality and energy consumption.
- Why an LoS/NLoS model is more realistic than a free-space-only link model.
- Why the VAE encoder/decoder is conditioned on the selected discrete action.
- Why simple rounding can make hybrid-action learning unstable.
- How the state and action dimensions scale with the number of UAVs.
- Why the Kubernetes testbed does not prove performance under real flight and radio conditions.

## 5. Compare it with Paper 01

| Interview prompt | Paper 01 | Paper 02 |
|---|---|---|
| Central problem | Vehicle mobility and edge coverage | Computing/link reliability and hybrid action decisions |
| Prediction component | Transformer predicts vehicle positions | No comparable Transformer mobility-prediction stage is proposed |
| RL component | MAPPO | TD3 with dependence-aware latent action representation |
| Objective | Joint delay and energy optimization | Long-term task success rate |
| Decision timing | Mobility-informed offloading | Decision triggered by task arrival |

## 6. A useful whiteboard sequence

1. Draw the UE, multiple UAVs, and edge-cloud server.
2. Show the three destinations for one task.
3. Draw the failure points: computing node and wireless transmission link.
4. Explain the deadline condition and task-success-rate objective.
5. Show the hybrid action: discrete destination + continuous UAV positions + UE transmit power.
6. Draw embedding + conditional VAE + TD3 actor/twin critics.
7. Finish with the two evaluation modes: simulation and Kubernetes/TCP-IP testbed.

## 7. Personal answer practice

Before using this in an interview, write your own answers to:

- Which part of the paper did I understand most deeply?
- Which technical idea took the most effort to understand?
- What is one limitation I can defend with evidence from the paper?
- What experiment would I run next, and why?
- How is this paper different from the first paper in my research portfolio?
