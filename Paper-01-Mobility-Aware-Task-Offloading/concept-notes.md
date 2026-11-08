# Concept Notes — Paper 01

These notes explain the ideas needed to understand the paper. The first description in each section stays close to the paper; the example is an intuition aid.

## 1. The problem setting

### Internet of Vehicles (IoV)

The **Internet of Vehicles** is a connected transportation environment in which vehicles communicate with network infrastructure and services. In this paper, vehicles generate computational tasks while moving through a road network.

**Intuition:** A connected vehicle may need to process information from sensors or in-vehicle applications quickly. Some tasks may be too demanding to execute locally within the required time.

### Mobile Edge Computing (MEC)

**Mobile Edge Computing** places computing resources near the devices that generate data, rather than sending every task to a distant centralized cloud. The paper considers edge servers as nearby computing destinations.

**Why it helps:** Shorter network paths can reduce communication delay. However, edge servers have finite computing capacity and limited geographic coverage.

### Roadside/edge server

An edge server is a ground-based computing node with a location, coverage radius, processing resources, and communication bandwidth. The paper models multiple edge servers.

### Unmanned Aerial Vehicle (UAV) as an edge node

A **UAV** is used as an auxiliary aerial edge node. Since it can move, it may provide computing support when a vehicle is expected to move outside the coverage area of a ground edge server.

**Important:** The UAV is not merely a communication relay in this model; it is also treated as a computing resource for offloaded tasks.

### Computation offloading

**Computation offloading** is the decision about where a task should execute. The paper considers three broad choices:

1. Execute on the vehicle itself.
2. Offload to a ground edge server.
3. Offload to a UAV.

The decision depends on the task's requirements and the resources, distances, and timing constraints of each possible destination.

## 2. Why vehicle mobility makes offloading difficult

A vehicle's location changes over time. It may move between the coverage areas of different edge servers. If a task is being transferred or processed while the vehicle moves away, handovers and communication changes can add delay or disrupt the service.

The paper's central insight is to predict the vehicle's future position and use that prediction when deciding whether a UAV should be prepared as an alternative edge node.

## 3. Delay-sensitive tasks

A **delay-sensitive task** must finish within a specified time limit. The paper models total task delay as the sum of computation delay and transmission delay:

\[
T_{\text{total}} = T_{\text{computation}} + T_{\text{transmission}}.
\]

- **Computation delay:** Time spent executing the task on the selected computing resource.
- **Transmission delay:** Time associated with sending the task to the edge server or UAV. The paper also discusses returning the result, while stating that return delay is generally negligible compared with upload delay in its model.

A task that misses its time constraint can negatively affect the application's Quality of Service (QoS).

## 4. Energy consumption

The paper considers both computation energy and transmission energy:

\[
E_{\text{total}} = E_{\text{computation}} + E_{\text{transmission}}.
\]

UAV movement also matters because a drone has a finite energy budget. The optimization must therefore consider whether a UAV can reach the vehicle in time without violating its energy constraint.

## 5. Transformer-based mobility prediction

A **Transformer** is a neural-network architecture that uses attention mechanisms to model relationships among elements in a sequence. In this paper, the Transformer receives a sequence of historical vehicle states containing:

- Position \(p_t\)
- Speed \(v_t\)
- Direction \(d_t\)

It predicts future vehicle positions. The paper describes input embedding, positional encoding, multi-head self-attention, feed-forward layers, and a prediction loss based on mean squared positional error.

**Intuition:** Instead of assuming a vehicle will remain where it is now, the system estimates where it is heading and uses that estimate to plan offloading.

## 6. Multi-Agent Reinforcement Learning (MARL)

In **multi-agent reinforcement learning**, multiple decision-making agents learn through interaction with a shared environment. In this paper, vehicles are the agents making task-offloading decisions. Their actions affect the performance of the overall vehicular network.

### Markov Decision Process (MDP)

The paper describes the offloading environment using four elements:

| Element | Meaning in this paper |
|---|---|
| State | Global information about vehicles, tasks, edge servers, and UAVs |
| Observation | Information available to an individual vehicle/agent, including its task and local vehicle characteristics |
| Action | The agent's offloading decision |
| Reward | A system-level reward that penalizes total delay and total energy consumption |

### MAPPO

**Multi-Agent Proximal Policy Optimization (MAPPO)** is the multi-agent policy-optimization method used in the second stage. The paper describes Actor and Critic networks:

- **Actor:** Uses an agent's observation to select an action (an offloading decision).
- **Critic:** Uses global state information to estimate value and help evaluate the Actor's decisions.
- **Advantage:** Compares the observed return with the Critic's value estimate, helping indicate whether an action was better than expected.
- **Clipped policy objective:** Limits the size of policy updates to support more stable learning.

The paper's setup uses a global reward to encourage individual agents to make decisions that benefit the overall system.

## 7. Datasets and baselines

### NGSIM

The first stage uses the **Next Generation Simulation (NGSIM) Vehicle Trajectories and Supporting Data**. The paper says this data contains vehicle position coordinates and longitudinal/lateral velocity components across time slots.

### Synthetic Vehicular 5G Dataset

The second stage uses a synthetic vehicular 5G dataset containing task sizes, locations of edge servers and UAVs, and computing-resource information. The paper uses this information together with predicted trajectories to evaluate offloading decisions.

### Comparison methods

| Stage | Proposed approach | Comparison methods |
|---|---|---|
| Mobility prediction | Transformer | LSTM, GRU, RNN |
| Computation offloading | ECMHI | MAPPO, COMA, MATD3 |

## 8. Age of Information (AoI)

**Age of Information** is a measure related to how fresh or up-to-date information is at its destination. The paper identifies AoI, along with security-aware offloading, as a direction for future work in MEC-enabled 6G networks.

## 9. A simple end-to-end example

Imagine a connected vehicle travelling toward the edge of a roadside server's coverage area:

1. The vehicle has a task that must finish within a strict time limit.
2. Historical position, speed, and direction data are passed to the Transformer.
3. The Transformer predicts that the vehicle will soon be outside the current server's coverage.
4. The system checks whether a UAV can reach the predicted vehicle position in time and has enough resources.
5. MAPPO-based agents make offloading decisions with the predicted mobility information available.
6. The system evaluates the resulting delay and energy use while observing the resource, timing, and UAV-energy constraints.

This example is an explanatory simplification of the paper's model, not a separate experiment reported by the authors.

## Quick self-check

You should be able to answer these without looking:

- [ ] Why is offloading harder when vehicles move quickly?
- [ ] What role does the UAV play beyond communication?
- [ ] What inputs and outputs are used by the Transformer stage?
- [ ] Why does the second stage use multi-agent reinforcement learning?
- [ ] What are the three offloading destinations?
- [ ] Why are delay and energy both included in the objective?
- [ ] Which dataset belongs to which stage?
