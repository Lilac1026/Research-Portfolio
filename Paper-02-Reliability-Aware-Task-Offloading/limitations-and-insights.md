# Limitations, Critical Insights, and Open Questions — Paper 02

This file deliberately separates statements made by the authors from independent observations that can be raised during a literature review or interview.

## 1. Points explicitly acknowledged by the authors

### Centralized coordination

The system is not fully decentralized. The edge-cloud server controls task-offloading and UAV-trajectory decisions using distributed information. The authors explain that UAVs have limited computing and energy resources, and a fully decentralized design would increase their local decision overhead and may rely on less complete local information.

### Decentralized system as future work

The conclusion notes that fully decentralized systems may provide flexibility and robustness. The authors identify deeper research into completely decentralized systems as future work.

### Real-life experimental validation

The work includes simulations and a Kubernetes-based testbed with TCP/IP communication. The authors nevertheless identify real-life experimental testing as future work. Therefore, the testbed should not be mistaken for an outdoor trial with actual UAV flight and real radio-channel dynamics.

## 2. Critical observations for further study

The following are analysis questions derived from the modeling and evaluation choices. They are not direct quotations or claims by the authors.

### A. Task-driven control and traffic patterns

The task-driven model can avoid waiting for fixed time-slot boundaries and reduce the number of decisions. However, its behaviour under highly bursty or very high task-arrival rates deserves investigation. A sequence of immediate decisions may still create a heavy control load or queueing pressure when many tasks arrive close together. The paper's discussion also uses both the phrases “low delay requirements” and “stringent latency requirements” when describing suitable scenarios. This wording is not fully consistent, so it is safer to retain the clearly stated low task-frequency condition and test latency conditions rather than infer one universal operating range.

**Question:** How does TRO compare with time-driven or event-triggered alternatives under low, high, and bursty task-arrival rates, and across different deadline regimes?

### B. Failure-model assumptions

The paper models computing and transmission failures using constant failure-rate Poisson processes and assumes a one-off fault that does not affect later tasks after recovery. Real systems may experience correlated failures, long repair periods, battery-related outages, changing interference, or common-cause failures affecting several UAVs.

**Question:** How robust is the learned policy if failure rates change over time or failures are correlated?

### C. Centralized controller dependency

The edge-cloud controller has a broad view of system information. In disaster or damaged-infrastructure scenarios—the motivation for UAV-assisted MEC—the controller or backhaul connection itself might be unavailable or delayed.

**Question:** Could a hierarchical controller or decentralized fallback policy preserve service when the edge-cloud coordinator becomes unreachable?

### D. UAV energy and movement realism

The model accounts for flight energy, speed limits, and collision separation. A real UAV also faces battery state uncertainty, wind, path-planning restrictions, take-off/landing constraints, payload changes, no-fly zones, and safety requirements.

**Question:** How much would these constraints change the trajectory and offloading policy?

### E. Communication model and environment

The probabilistic LoS/NLoS model is more expressive than a free-space-only model, but actual radio conditions may vary with buildings, weather, mobility, antenna orientation, interference, and frequency band.

**Question:** Does the policy retain its performance when the channel model is calibrated from measurements or changes from the training distribution?

### F. What the testbed validates

The Kubernetes-based testbed validates application-layer deployment and TCP/IP communication between nodes. It does not by itself validate actual UAV flight, aerial channel conditions, battery behaviour, or field-level reliability.

**Question:** What instrumentation and experimental setup would be required for an outdoor multi-UAV validation?

### G. Optimization objective

The explicit objective is long-term task success rate. This is well aligned with deadline-sensitive services, but two policies with similar success rates could still have different energy use, latency distributions, fairness, or resource utilization. The paper imposes energy constraints and models delay, but further multi-metric reporting may be useful in deployment decisions.

**Question:** How would a system operator choose between two policies with the same success rate but different energy consumption or tail latency?

### H. Scale and complexity

The paper provides asymptotic per-iteration complexity for the representation and actor-critic networks. Real runtime will also depend on replay-buffer size, network communication, model update frequency, hardware, and number of users/tasks.

**Question:** What are the measured training/inference latencies and memory requirements as the number of UAVs and UEs grows?

## 3. What is technically interesting about the contribution?

The notable part is not merely “using deep reinforcement learning for UAVs.” The paper identifies a specific representation problem: one action contains a discrete destination and continuous control variables, and these variables are dependent.

A simple continuous-to-discrete rounding approach can produce unstable learning. Ignoring continuous variables also loses important controls. The dependence-aware latent representation attempts to learn a shared structure for both action types, while TD3 performs policy learning in that latent space.

This is a useful example of **changing the action representation to make a difficult optimization problem more learnable**, rather than only changing the reward function or adding a larger policy network.

## 4. How Paper 02 differs from Paper 01

Paper 01 (ECMHI) predicts future vehicle locations with a Transformer and then uses MAPPO for offloading, targeting delay and energy in a highly mobile Internet-of-Vehicles scenario.

Paper 02 (TRO) does not use that Transformer-MAPPO pipeline. It models task-level computing and transmission reliability, then jointly chooses task destination, UAV positions, and UE transmit power using a dependence-aware latent representation and TD3. Its principal objective is task success rate.

This contrast is useful in a literature review because it shows that UAV-assisted MEC can be formulated around different system bottlenecks: **mobility uncertainty** in one study and **execution/transmission unreliability plus hybrid action decisions** in the other.

## 5. Questions to investigate before claiming a research gap

1. What recent approaches combine task-arrival-triggered offloading with online reliability estimation?
2. How are correlated UAV failures, common-cause wireless outages, and controller failures modeled in newer systems?
3. Can hybrid-action representation methods be compared fairly under identical environment and reward settings?
4. What happens when the task-arrival rate is high or tasks arrive in bursts?
5. Can a decentralized or hierarchical extension retain task-success performance when edge-cloud connectivity is intermittent?
6. What real UAV field measurements are available to calibrate the mobility, energy, and LoS/NLoS models?

These are research questions for further searching and experimentation, not established gaps in the entire literature.

## 6. Safe critical-review wording

A balanced statement for a presentation or interview:

> The paper makes reliability an explicit part of the offloading decision and addresses the challenge of jointly selecting discrete and continuous control variables. Its simulation and Kubernetes-based testbed results are encouraging. However, the study still uses modeled failure processes and centralized edge-cloud coordination, and it identifies fully decentralized operation and real-life experiments as future directions. Evaluating performance under bursty task arrivals, correlated failures, and actual UAV flight conditions would be useful next steps.
