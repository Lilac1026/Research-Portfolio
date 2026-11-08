# Limitations, Critical Reading, and Research Insights — Paper 01

This file separates three things:

1. **What the authors explicitly state** in the paper.
2. **What can be observed from the modeling and evaluation design.**
3. **Questions that a careful reader could investigate further.**

Keeping these separate prevents a reader's interpretation from being presented as an author-stated result.

## 1. Strengths of the approach

### 1.1 Prediction is connected to a real network decision

The Transformer is not used as an isolated trajectory-prediction demonstration. Its predicted positions inform whether a vehicle may leave edge-server coverage and whether a UAV should be considered. This creates a clear link between prediction and downstream resource allocation.

### 1.2 The architecture considers more than one performance metric

The method considers both delay and energy consumption. This matters because an offloading choice that reduces task delay may consume additional communication, computation, or UAV-movement energy.

### 1.3 Multiple resource locations are modeled

The system can use local vehicle computing, ground edge servers, and UAV computing. This gives the offloading policy more flexibility than a model limited to one fixed server type.

### 1.4 The two stages are evaluated against baselines

The paper compares Transformer with LSTM, GRU, and RNN for prediction, and compares ECMHI with MAPPO, COMA, and MATD3 for offloading. This helps assess the two components separately.

## 2. Assumptions and scope boundaries stated in the paper

### 2.1 Simplified wireless communication model

The communication model is based on large-scale path loss and geometric distance. It omits small-scale fading. The authors justify this by the difference in time scales, their system-level focus, and the additional computational complexity that small-scale fading would introduce.

**Why this matters:** Real wireless links can fluctuate. A model that averages out fine-grained channel variation may not capture every short-term communication disruption.

### 2.2 Negligible return delay

The paper notes that the delay for returning a processed result is generally negligible compared with uploading the task. This is an assumption within the model; it may not hold for applications where result payloads are large or downlink capacity is constrained.

### 2.3 Synthetic dataset in the offloading stage

The mobility-prediction stage uses NGSIM vehicle trajectory data, while the second stage uses a Synthetic Vehicular 5G Dataset. Results from the offloading stage should therefore be interpreted in the context of that modeled dataset and its assumptions.

### 2.4 No real-world UAV field deployment is reported

The experimental section describes Python-based experiments and a computing environment. The paper does not report a physical UAV field trial. The reported evidence therefore supports the method within the paper's experimental setup, not a claim that it has been validated in live roadside deployment.

## 3. Details that deserve closer examination

These are critical-reading questions, not proven flaws.

### 3.1 Sensitivity to prediction errors

The authors report that Transformer prediction error is around the metre scale and introduce a 5 m safety margin by shrinking the boundary in advance. A useful follow-up question is how the offloading policy behaves when prediction errors are larger, asymmetric, or caused by abrupt manoeuvres.

### 3.2 Choice of delay-energy weights

The reward uses weights \(\alpha\) and \(\beta\), with \(\alpha+\beta=1\). The selected weights influence the trade-off between delay and energy. A reader could investigate how the results change under different weight settings and whether the preferred policy changes across application types.

### 3.3 Generalization beyond the tested settings

The paper evaluates different vehicle counts and task sizes, but a deployment-oriented review could ask how robust the approach is to different road layouts, traffic patterns, edge-server placements, UAV fleets, and wireless conditions.

### 3.4 Clarifying the MAPPO training procedure

Algorithm 2 describes storing experience in a buffer and sampling a mini-batch. The paper does not fully explain the buffer lifecycle and sampling schedule in the presented pseudocode. A careful implementation review should clarify exactly how trajectories are collected and reused during policy updates.

### 3.5 UAV coordination and operational constraints

The model includes UAV travel time, computing resources, and energy constraints. A further study could examine practical issues such as flight restrictions, obstacle avoidance, battery replacement, weather, safe separation between UAVs, and the cost of maintaining aerial edge infrastructure. These are possible deployment considerations, not variables evaluated in the paper.

## 4. Future work identified by the authors

The conclusion highlights **Age of Information (AoI)** and **security-aware service offloading** in MEC-enabled 6G networks as future research directions.

AoI concerns the freshness of information. Combining freshness and security with delay, energy, and mobility would create a broader optimization problem.

## 5. My research takeaways

Use these as a starting point and rewrite them after you have personally studied the paper:

- A good prediction model becomes more useful when its output is connected to a downstream system decision.
- Dynamic environments require decisions to account for what may happen next, not only what is observable now.
- Optimization under real constraints is different from minimizing a single metric in isolation.
- Multi-agent decisions can have local effects and system-wide consequences; reward design influences whether agents cooperate.
- Evaluation should be read together with the dataset, assumptions, baselines, and scope of the experiments.

## 6. Questions worth asking in a research discussion

1. How does ECMHI behave when the trajectory predictor is uncertain rather than producing a single predicted path?
2. Could prediction uncertainty be passed to MAPPO so that offloading decisions become risk-aware?
3. How should the system choose \(\alpha\) and \(\beta\) when different vehicles run applications with different latency and energy priorities?
4. What happens when several vehicles need the same UAV at the same time?
5. How would weather, UAV flight limits, and battery constraints affect the architecture?
6. Could AoI be incorporated into the reward alongside delay and energy?
7. How would the model behave under more realistic channel variation and intermittent connectivity?
8. What changes would be needed to test the system on a real UAV-edge platform?

## 7. Bottom-line assessment

The paper presents a coherent two-stage strategy: predict vehicle mobility, then use that information in multi-agent offloading optimization. Its most interesting design choice is the integration of proactive mobility prediction with UAV-assisted edge computing. The main caution is to interpret its performance claims within the stated simulation/data setup and communication assumptions. A strong reader should be able to explain both the benefit of the integration and the conditions that remain to be tested.
