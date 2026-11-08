# Technical Deep Dive — Paper 01

This file collects the central notation, equations, and algorithmic ideas. The explanations are study interpretations of the equations and definitions in the paper; they are not a re-implementation.

## 1. Important notation

| Symbol | Meaning in the paper |
|---|---|
| \(V\) | Set of local vehicles |
| \(M\) | Set of ground edge servers |
| \(U\) | Set of UAVs |
| \(T\) | Set of tasks |
| \(p_t\) | Vehicle position at time \(t\) |
| \(v_t\) | Vehicle speed at time \(t\) |
| \(d_t\) | Vehicle direction at time \(t\) |
| \(R_{mi}\) | Coverage radius of edge server \(m_i\) |
| \(F\) | Computing-resource quantity (the exact subscript identifies the vehicle/server/UAV) |
| \(\tau\) | Maximum task-delay constraint |
| \(T_n^{Com}\) | Computation delay for task \(n\) |
| \(T_n^{Trans}\) | Transmission delay for task \(n\) |
| \(T_n^{Total}\) | Total delay for task \(n\) |
| \(E_n^{Com}\) | Computation energy for task \(n\) |
| \(E_n^{Trans}\) | Transmission energy for task \(n\) |
| \(E_n^{Total}\) | Total energy for task \(n\) |
| \(T^{UAV}\) | UAV travel time |
| \(\alpha,\beta\) | Reward weights for delay and energy, with \(\alpha+\beta=1\) |
| \(\gamma\) | Discount factor for future rewards |

## 2. Delay model

The paper separates task delay into computation and transmission components (Eq. 7):

\[
T_n^{Total}=T_n^{Com}+T_n^{Trans}.
\]

### Computation delay

The computation delay depends on where the task is executed. The paper expresses it using task computation requirements and the processing rate of the selected resource (vehicle, edge server, or UAV; Eq. 5).

**Interpretation:** For the same amount of computation, a resource with a higher processing rate can generally finish the computation in less time, all else being equal.

### Transmission delay

The paper models transmission delay using task size and the vehicle-to-edge/UAV transmission rate (Eq. 6). The communication rate depends on bandwidth, transmission power, channel gain, and noise in the modeled link (Eq. 4).

The paper notes that the return delay for the processed result is generally negligible compared with upload delay in its model. This is a modeling assumption, not a universal property of every application.

## 3. Energy model

The total task energy is represented as (Eq. 10):

\[
E_n^{Total}=E_n^{Com}+E_n^{Trans}.
\]

The paper models computation energy according to the selected computing resource (Eq. 8) and transmission energy using transmission power and transmission time (Eq. 9). UAV movement energy is also included in the UAV energy constraint.

**Why include energy?** A decision that reduces delay by using a UAV may still be undesirable if it consumes too much energy or leaves insufficient energy for UAV movement and operation.

## 4. Mobility prediction

### Historical input and predicted output

\[
S=[(p_1,v_1,d_1),\ldots,(p_T,v_T,d_T)]
\]

\[
P_{future}=[p_{T+1},\ldots,p_{T+k}]
\]

The model predicts future positions from historical position, speed, and direction information (Eqs. 16–17).

### Self-attention

The paper gives the scaled dot-product attention expression (Eq. 18):

\[
Attention(t,\tau)=Softmax\left(\frac{Q_tK_\tau^T}{\sqrt{d}}\right).
\]

A conceptual reading:

- Query \(Q_t\) represents the time step whose representation is being updated.
- Key \(K_\tau\) represents another time step being compared with it.
- The dot product measures compatibility between query and key.
- Division by \(\sqrt d\) scales the scores.
- Softmax converts scores into normalized attention weights.

The attention weights are used to combine value representations (Eq. 19), and the result is processed by a feed-forward network (Eq. 20).

### Prediction loss

The Transformer is trained to minimize the mean squared error between predicted and ground-truth future positions (Eq. 21):

\[
\mathcal{L}=\frac{1}{k}\sum_{i=1}^{k}\left\|p_{T+i}-\hat p_{T+i}\right\|^2.
\]

Here, \(p_{T+i}\) is the actual future position and \(\hat p_{T+i}\) is the predicted position. Lower loss indicates that the predicted coordinates are closer to the ground-truth coordinates under this metric.

## 5. Why the mobility estimate changes the offloading decision

The paper calculates the predicted distance between a vehicle and an edge server, then checks it against the server's coverage radius (Eqs. 12–13). If the vehicle is expected to leave coverage, a UAV can be considered as an auxiliary edge node.

The UAV travel-time relation is (Eq. 14):

\[
T^{UAV}=\frac{D^{UAV}}{S_u},
\]

where \(D^{UAV}\) is the distance to the predicted vehicle position and \(S_u\) is UAV speed.

**Interpretation:** A UAV is only useful as a proactive option when it can reach the vehicle within the relevant time window and has sufficient computing and energy resources.

## 6. MAPPO formulation

### State, observation, and action

The global state includes information about vehicles, tasks, edge servers, and UAVs (Eq. 22):

\[
S=\{V,T,M,U\}.
\]

An individual vehicle's observation contains its own task and local information such as position, speed, direction, computing resources, and an energy-related coefficient (Eq. 23).

The joint action represents the offloading decisions of the vehicles (Eq. 24). Each decision selects local execution or an eligible edge/UAV destination under the model's constraints.

### Global reward

The paper defines a global reward that penalizes weighted total delay and energy (Eq. 25):

\[
R=-\left(\alpha\sum_{n=1}^{N_1}T_n^{Total}+\beta\sum_{n=1}^{N_1}E_n^{Total}\right),
\quad \alpha+\beta=1.
\]

The negative sign makes lower cost correspond to a higher (less negative) reward. The two weights express the relative priority of delay and energy.

### Return and advantage

The discounted return is given by (Eq. 26):

\[
R_t=\sum_{k=0}^{\infty}\gamma^k r_{t+k}.
\]

The paper estimates the advantage as (Eq. 27):

\[
A(o_t,a_t)=R_t-V(s_t).
\]

In intuitive terms, the advantage compares the return associated with an action to the Critic's estimate of the value of the global state. A positive advantage means the outcome was better than the value estimate; a negative advantage means it was worse.

### Actor and Critic updates

The paper describes a clipped policy objective (Eq. 29) to limit policy-update magnitude. It updates Actor parameters using policy-gradient ascent (Eq. 30). The Critic minimizes a squared value-estimation error (Eq. 31):

\[
L_c(\phi)=\mathbb{E}_t\left[(R_t-V_\phi(s_t))^2\right].
\]

The Critic parameters are updated by gradient descent (Eq. 32).

## 7. Optimization problem and constraints

The paper's optimization objective is to minimize both total delay and total energy (Eq. 15), subject to:

- **Timing constraint:** Task delay must be below the maximum allowed delay; a UAV must arrive before the vehicle leaves the relevant coverage region.
- **Computing-resource constraints:** Allocated computing resources must not exceed the capacities of vehicles, edge servers, and UAVs.
- **UAV choice constraint:** The model considers the UAV with the most available computing resources among those meeting the travel-time condition.
- **UAV energy constraint:** The UAV's task-related and movement energy must remain within its energy budget.

## 8. Experimental details to remember

| Item | Reported detail |
|---|---|
| Mobility dataset | NGSIM Vehicle Trajectories and Supporting Data |
| Mobility train / evaluation / test | 431,204 / 32,463 / 26,594 samples |
| Mobility baselines | LSTM, GRU, RNN |
| Offloading dataset | Synthetic Vehicular 5G Dataset |
| Offloading baselines | MAPPO, COMA, MATD3 |
| Metrics discussed | Prediction loss/error, reward, delay, energy consumption |
| Repeated task-size evaluation | More than 50 experiments in the same environment |
| Reported implementation environment | Python; Windows 10 64-bit; Intel Core i7-9750H 2.60 GHz; 16 GB RAM |

The paper's plots show comparative trends, but the article does not provide a complete numerical results table for every plotted point. Avoid inventing exact values when describing the figures.

## 9. Technical distinction to state correctly

Do not say that the Transformer chooses where a task should run. It predicts vehicle mobility. Do not say that MAPPO predicts vehicle coordinates. MAPPO is used for the offloading decision. The contribution comes from connecting the predicted positions to the second-stage decision process.
