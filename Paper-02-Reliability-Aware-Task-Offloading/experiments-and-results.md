# Experiments and Results — Paper 02

## 1. Evaluation approach

The authors evaluate the proposed **Task-driven Reliability-aware Offloading (TRO)** scheme in two ways:

1. Simulations to compare learning behaviour and task-success performance under controlled changes to system/task parameters.
2. A Kubernetes-based testbed in which nodes communicate using TCP/IP, to assess practicality beyond simulated communication between program instances.

The evaluation uses four alternative methods, including ablation variants designed to isolate the role of reliability and hybrid-action representation.

## 2. Simulation setup

The paper reports the following configuration:

| Parameter | Setting reported in the paper |
|---|---|
| Software | Python 3.9 and PyTorch 2.2 |
| Area | 400 m × 400 m square region |
| UAV computing capability | 10 Gigacycles per UAV |
| Maximum horizontal UAV speed | 25 m/s |
| Maximum vertical UAV speed | 10 m/s |
| Effective switched capacitance | `10^-28` |
| Noise power | -100 dBm for the modeled links |
| Path-loss constants | `a = 9.61`, `b = 0.16`, `eta_LoS = 1`, `eta_NLoS = 20` |
| Training batch size | 64 |
| Discount factor | 0.99 |
| Optimizer | Adam |

The article also gives ranges for UE computing capability and link bandwidth. Since the printed bandwidth unit is not consistently intuitive in the extracted text, these notes avoid restating those ranges as a precise unit-sensitive specification.

## 3. Comparison methods

| Method | What it represents | Why it is compared |
|---|---|---|
| **TDR** — Time-driven and reliability-aware | Uses time slots and models execution/transmission reliability, but does not consider UAV mobility in the described baseline | Compares task-driven decisions against time-driven control |
| **NCR** — No continuous variables representation | Represents discrete variables only and does not model their dependence on continuous variables | Tests the contribution of representing continuous action variables |
| **NHR** — No hybrid action representation | Uses simple rounding to turn continuous values into discrete values, without the proposed hybrid representation | Tests the dependence-aware latent representation |
| **NRA** — No consideration of reliability | Optimizes average task delay rather than task success rate and omits execution/transmission reliability | Tests the contribution of explicitly modeling reliability and optimizing task success |
| **TRO** — Proposed method | Uses task-driven decisions, reliability-aware objective/modeling, hybrid action representation, and TD3 | Full proposed approach |

NCR and NHR are ablation-style comparisons. The baseline labels should be expanded when discussing results, rather than treating them as unexplained acronyms.

## 4. Simulation findings

The paper reports the following qualitative trends. These are the supported findings from the text and plotted figures; exact point values should not be quoted unless they are independently digitized from the figures.

### 4.1 Convergence

The convergence plot compares TRO with the representation ablations. NHR has the weakest convergence behaviour because simple rounding introduces fluctuations. NCR converges faster because it has a simpler network and represents only discrete variables. TRO has a more complex representation because it handles both discrete and continuous variables, but the authors report that it still converges well.

### 4.2 Effect of task computing workload

As average task computing workload increases, the task-success rate decreases for all compared methods. More workload requires more computation time while available computing resources remain limited, so more tasks risk exceeding their deadlines. TRO generally performs better than the compared alternatives in the tested settings.

### 4.3 Effect of transmission size

As average task transmission size increases, task-success rate decreases. Larger tasks require more transmission time and have lower modeled transmission reliability over that longer interval.

### 4.4 Effect of deadline threshold

Increasing the allowed delay threshold increases task-success rate because fewer tasks are dropped for exceeding their deadlines. The success rate does not reach 1 in the reported setting because some tasks may still fail during execution or transmission.

### 4.5 Number of UEs and UAVs

- Increasing the number of UEs increases system workload. With limited computing resources, task delays increase and more tasks miss their deadlines, so success rate decreases.
- Increasing the number of UAVs adds computing resources. In the tested scenarios, computation delay falls and more tasks complete before their deadlines, so success rate increases.

### 4.6 Failure-rate parameter

As the UAV failure-rate parameter increases, failures occur more frequently under the model and the task-success rate decreases. This experiment directly illustrates why modeling reliability matters.

## 5. Kubernetes-based testbed

The authors explain that simulation nodes are program instances and their communication is simulated rather than performed over an actual TCP/IP network. To test the algorithm in a more realistic communication setup, they build a Kubernetes-based testbed.

The architecture has three layers:

### Model training layer

Contains the DRL model, replay buffer, and agent. The authors report implementing the algorithm using **Deep Java Library (DJL)**, a deep-learning framework for Java.

### Control layer

Runs as a Pod in Kubernetes. It configures the network service environment and generates tasks.

### Execution layer

Deploys nodes and makes task-offloading decisions within the application.

Nodes communicate over a TCP/IP network.

## 6. Kubernetes testbed findings

The testbed experiments vary task size and deadline threshold:

- Increasing task size reduces success rate significantly. In this setup, a larger task size increases both transmission size and computing workload.
- Increasing the delay threshold improves success rate, consistent with the simulation observations.

The testbed strengthens the practical evaluation compared with simulation-only communication. It should not be described as a full field trial with actual flying UAVs and outdoor radio conditions; the authors state that real-life experimental testing remains future work.

## 7. What can safely be claimed?

A defensible summary is:

> In the reported simulations and Kubernetes-based TCP/IP testbed, TRO achieves better task-success performance than the four compared alternatives across the tested settings. Its advantage is linked to task-triggered decisions, explicit execution/transmission reliability modeling, and the proposed representation of coupled discrete and continuous actions.

Avoid claims such as:

- “TRO guarantees 100% task success.” The paper explicitly shows that success rate remains below 1 in scenarios with failures.
- “The method has been field-tested using real UAV flights.” The described testbed is Kubernetes-based; real-life experimental testing is future work.
- “TRO always beats every possible offloading method.” The paper evaluates a defined set of four alternatives in its experimental setup.
- “The paper proves exact percentage improvements in all scenarios.” The article primarily presents plotted trends; use exact percentages only when a numerical value is explicitly reported or carefully measured from a figure.

## 8. Figures worth studying

| Figure | What it helps explain |
|---|---|
| Fig. 1 | System model with UEs, UAVs, and edge cloud |
| Fig. 2 | Convergence of the proposed and ablation algorithms |
| Fig. 3 | Success rate as computing workload, transmission size, and deadline threshold change |
| Fig. 4 | Success rate as UE count, UAV count, and failure-rate parameter change |
| Fig. 5 | Three-layer Kubernetes testbed architecture |
| Fig. 6 | Testbed success rate under task-size and deadline-threshold changes |

The figures are in the publisher's paper. The repository notes describe them rather than reproducing the article's figures.
