# Cross-Paper Comparison — Papers 01, 02, and 03

This comparison highlights each paper's own problem formulation and methods. The papers do not study an identical system, so their numerical results should not be compared directly.

## 1. At-a-glance comparison

| Dimension | Paper 01 — ECMHI | Paper 02 — TRO | Paper 03 — DETER |
|---|---|---|---|
| Main setting | UAV-MEC-assisted Internet of Vehicles | Multi-UAV-assisted MEC with UEs and edge cloud | Multi-owner service-oriented sensor-cloud |
| Main problem | Vehicle mobility can make fixed edge coverage unstable during offloading | Tasks can fail due to computing-unit and wireless-link unreliability | Selfish sensor-owners can undermine multi-hop forwarding and Se-aaS quality |
| Main decision trigger | Mobility prediction followed by offloading decisions | Task arrival, rather than fixed time-slot boundaries | Service request plus periodic trust updates / route selection |
| Main learning / decision method | Transformer for mobility prediction + MAPPO for offloading | Dependence-aware hybrid-action representation + TD3 | Beta-reputation trust, utility-based next-hop selection, Stackelberg pricing game |
| Main decision variables | Offloading decisions informed by predicted vehicle positions | Offloading destination, UAV positions, UE transmit power | Candidate next hops, end-to-end path, and payments to sensor-owners |
| Main optimization emphasis | Delay and energy under task/resource constraints | Long-term task success rate | Trust-aware service quality and economic utility for SCSP and owners |
| Key evidence | NGSIM mobility data and synthetic vehicular 5G offloading evaluation | Simulations and Kubernetes/TCP-IP testbed | MATLAB simulation with 400–800 nodes, four owners, and DOP/TERP baselines |

## 2. What each paper contributes

### Paper 01 — Mobility-aware offloading

This paper predicts future vehicle positions using a Transformer and passes the predicted mobility information to a MAPPO-based offloading stage. Its core insight is that mobility prediction can support proactive UAV/edge scheduling rather than reacting only to current coverage conditions.

### Paper 02 — Reliability-aware offloading

This paper argues that delay minimization alone does not guarantee task completion. It models computing and transmission failures, uses task-arrival-driven decisions, and learns a hybrid representation for discrete offloading choices and continuous UAV/power controls before applying TD3.

### Paper 03 — Trust-enforcing sensor-cloud pricing

This paper is not another UAV task-offloading algorithm. It studies a service-oriented sensor-cloud with heterogeneous sensors from multiple owners. DETER calculates distributed node trust and centralized owner trust, then links trust-aware route selection with dynamic pricing through a Stackelberg game.

## 3. Shared research themes

Despite the different system settings, several broad themes recur:

- **Resource constraints:** computing capacity, energy, communication quality, or sensor-node availability shapes the decision.
- **Distributed participants:** vehicles, UEs, UAVs, sensor nodes, and owners contribute resources but do not all have the same information or incentives.
- **Decision quality under uncertainty:** mobility, failures, changing workloads, or potentially selfish behavior can affect service outcomes.
- **Service-level outcomes:** the methods ultimately aim to improve a service delivered to users, not merely optimize an isolated algorithmic score.

## 4. Important differences

1. **The objective differs.** Paper 01 emphasizes delay and energy; Paper 02 maximizes task success; Paper 03 balances trust, routing quality, owner payments, and SCSP profit.
2. **The learning/decision paradigm differs.** Papers 01 and 02 use deep reinforcement learning in UAV-MEC. Paper 03 combines reputation modeling, utility functions, and game theory.
3. **The network abstraction differs.** Papers 01 and 02 involve computation tasks offloaded among local/edge/UAV/cloud resources. Paper 03 routes sensed data through heterogeneous sensor nodes to support a Sensors-as-a-Service offering.
4. **The evaluation evidence differs.** Paper 01 uses trajectory and synthetic vehicular data; Paper 02 adds a Kubernetes/TCP-IP testbed; Paper 03 reports MATLAB simulation.

## 5. A possible synthesis question

The following is an independent research question inspired by the three readings, not a claim made by the paper authors:

> How could a networked edge-service system jointly consider mobility-aware resource availability, task or link reliability, and trust/incentive signals when selecting service providers and routes?

A possible decomposition would be:

- use mobility prediction to estimate where edge resources will be available;
- incorporate reliability estimates into service-success predictions;
- use reputation/incentive mechanisms when resources are owned by independent parties; and
- evaluate the combined design using service-level metrics, overhead, energy, and economic fairness.

This question is broad and would need a precise system model before it could become a research proposal.

## 6. Questions for personal synthesis

1. Which of the three papers has the clearest optimization objective, and why?
2. What types of uncertainty or risk does each paper represent explicitly?
3. Which papers rely on centralized coordination, and what trade-offs does that create?
4. How do the evaluation methods affect the strength of each paper's practical claims?
5. Which assumptions could fail in a real deployment?
6. What is one new research question that emerges only after comparing all three papers?
