# Technical Deep Dive — Paper 02

This file collects the mathematical and algorithmic details most useful for a technical discussion. Symbols follow the paper where possible. The descriptions beneath equations are explanatory notes, not a replacement for the full derivations in the article.

## 1. Task and destination notation

A task is:

\[
\mathcal{T}_k=(c_k,u_k,t_k,m_k,d_k)
\]

- \(c_k\): computation workload in CPU cycles.
- \(u_k\): amount of data to transmit.
- \(t_k\): task arrival time.
- \(m_k\): UE generating the task.
- \(d_k\): maximum delay allowed for the task.

The destination is:

\[
i_k\in\{0,1,\ldots,N,N+1\}
\]

`0` denotes local execution, `1...N` a UAV, and `N+1` the edge-cloud server. The task is offloaded as a whole.

## 2. UAV mobility constraints

The 3D coordinate of UAV `n` at task arrival `k` is:

\[
\mathbf{w}_n(k)=[x_n(k),y_n(k),z_n(k)]^T
\]

The horizontal and vertical movement limits are expressed as:

\[
\Delta v_n(k)=\|\mathbf{v}_n(k+1)-\mathbf{v}_n(k)\|\le L_h^{\max}\Delta k
\]

\[
\Delta z_n(k)=|z_n(k+1)-z_n(k)|\le L_v^{\max}\Delta k
\]

where \(\Delta k\) is the elapsed time between the arrivals of tasks `k` and `k+1`; \(L_h^{\max}\) and \(L_v^{\max}\) are the maximum horizontal and vertical speeds.

Collision avoidance requires:

\[
\|\mathbf{w}_n(k)-\mathbf{w}_j(k)\|\ge D_{\min},\quad n\ne j
\]

The paper models UAV flight power as related to speed:

\[
P_n^{fly}(v)=\frac{W_n}{2}v^2
\]

and obtains flight energy over the interval using flight power multiplied by elapsed time. This means UAV positioning and task placement have an energy cost.

## 3. Reliability equations

### 3.1 Computing-node reliability

Let failures at computing node `j` follow a Poisson process with failure rate \(\alpha_j\). The probability of observing `h` failures during computation time \(t_{j,k}^{co}\) is modeled as:

\[
\Pr\{H_j(t_{j,k}^{co})=h\}=\frac{(\alpha_jt_{j,k}^{co})^h}{h!}e^{-\alpha_jt_{j,k}^{co}}
\]

Successful execution under this model requires zero failures (`h=0`):

\[
R_{j,k}^{c}=e^{-\alpha_jt_{j,k}^{co}}
\]

### 3.2 Transmission-link reliability

For a UE-to-node link with failure rate \(\beta_{m,j}\) and transmission duration \(t_{m,j,k}^{tr}\):

\[
R_{m,j,k}^{t}=e^{-\beta_{m,j}t_{m,j,k}^{tr}}
\]

### 3.3 End-to-end reliability for offloaded tasks

For UAV execution, the modeled reliability is the product of the UAV computing reliability and the UE-to-UAV link reliability:

\[
R_k^{edge}=R_{i_k,k}^{c}\,R_{m_k,i_k,k}^{t}
\]

Equivalently, because both factors are exponentials, the combined exponent includes the computing-failure term and transmission-failure term. The cloud case uses the edge-cloud computing reliability and UE-to-EC transmission reliability. The local case uses local computing reliability without an offloading link.

**Interpretation:** Longer compute or transmission time creates a longer interval in which a failure can occur under the assumed constant-rate model.

## 4. LoS/NLoS communication model

For UE-to-UAV links, the paper does not rely only on a free-space path-loss expression. It defines probabilities for LoS and NLoS communication based on the UAV elevation angle. Average path loss is formed from the LoS and NLoS path-loss terms weighted by their probabilities:

\[
\bar{PL}_{m,n}(k)=PL_{m,n}^{LoS}(k)P_{m,n}^{LoS}(k)+PL_{m,n}^{NLoS}(k)P_{m,n}^{NLoS}(k)
\]

The channel gain is modeled as the inverse of average path loss:

\[
g_{m,n}(k)=\frac{1}{\bar{PL}_{m,n}(k)}
\]

A Shannon-style rate model then uses bandwidth, UE transmission power, channel gain, and noise power. The transmission time is data size divided by the relevant rate:

\[
t_{m,n,k}^{tr}=\frac{u_k}{r_{m,n}^{U2A}(k)}
\]

The exact model also distinguishes UE-to-UAV and UE-to-edge-cloud links and uses different bandwidth/noise parameters for the two links.

## 5. Delay and deadline condition

The destination-specific completion time is selected from the local, UAV, and cloud service-delay models. In compact form:

\[
T(k)=\mathbf{1}_{i_k=0}T^l(k)
+\mathbf{1}_{0\ne i_k\ne N+1}T^e(k)
+\mathbf{1}_{i_k=N+1}T^c(k)
\]

A task must satisfy:

\[
T(k)\le d_k
\]

The paper models local and UAV computation queues. UAV queues are FIFO, and cloud computation is modeled as having abundant resources/no computation queue. Cloud offloading can still be slower because transmission distance is greater.

## 6. Task-success-rate objective

The paper defines the cumulative success rate as:

\[
ST(k)=\frac{|K_{succ}(k)|}{|K_{succ}(k)|+|K_{drop}(k)|+|K_{fail}(k)|}
\]

where:

- \(|K_{succ}(k)|\): successful tasks up to task arrival `k`.
- \(|K_{drop}(k)|\): tasks that exceeded their deadlines and were dropped.
- \(|K_{fail}(k)|\): tasks that failed during execution or transmission.

The long-term optimization objective is:

\[
\max\lim_{k\to\infty}ST(k)
\]

subject to transmit-power limits, valid offloading destinations, UAV movement/collision constraints, and UE/UAV energy budgets.

**Important distinction:** This objective is not simply `minimize average delay`. Delay still matters because of deadlines, but the explicit long-term objective is task success rate.

## 7. MDP definition

The problem is formulated as a Markov Decision Process indexed by task arrivals.

### State

The paper's state includes:

- Current waiting delay/workload of the UE generating the task.
- Computing capability of that UE.
- Waiting delays of all UAV computation queues.
- Transmission delays from the UE to UAVs and to the edge cloud.
- Computing capabilities of UAVs.
- Current task workload and its deadline.

The stated state dimension is:

\[
\dim(s)=3N+6
\]

### Action

The action is defined in the article as a joint selection of offloading destination, UAV position, and UE transmit power:

\[
a(k)=\{i_k,\mathbf{w}(k),p(k)\}
\]

There are `3N` continuous position coordinates, one continuous transmit-power variable, and one discrete destination variable. The article gives the total action dimension as `3N + 2` and calls it a discrete-continuous hybrid space.

### Reward

The task feedback is:

\[
r_k=\begin{cases}+1,&\text{successful task}\\-1,&\text{dropped or failed task}\end{cases}
\]

The paper uses a shared team reward across nodes, encouraging cooperative system-level decisions.

## 8. Dependence-aware latent representation

Let a hybrid action be denoted by \(a=(i,v)\), where `i` is the discrete destination and `v` collects the continuous controls.

### 8.1 Discrete embedding

A trainable embedding table is:

\[
G_\omega\in\mathbb{R}^{(N+2)\times l_1}
\]

Each possible destination `i` is mapped to a trainable vector:

\[
g_{\omega,i}=G_\omega(i)
\]

### 8.2 Conditional VAE encoder and decoder

The encoder is conditioned on the current state and the discrete-action embedding:

\[
q_\phi(z\mid v,s,g_{\omega,i})
\]

It maps the continuous control `v` to a latent variable \(z\in\mathbb{R}^{l_2}\), modeled as a Gaussian with learned mean and standard deviation.

The decoder reconstructs the continuous controls:

\[
\tilde v=q_\psi(z,s,g_{\omega,i})
\]

The discrete destination is recovered by a nearest-neighbour lookup in the embedding table:

\[
i=\arg\min_{i'\in I}\|g_{\omega,i'}-g\|_2
\]

This representation creates a continuous latent action space of dimension \(l_1+l_2\), while retaining the discrete choice and its relationship with the continuous controls.

### 8.3 Representation objectives

The VAE-related loss combines squared reconstruction error and KL divergence:

\[
L_V(\psi,\phi,\omega)=\mathbb{E}\left[\|v-\tilde v\|_2^2+D_{KL}\left(q_\phi(\cdot\mid v,s,g_{\omega,i})\,\|\,\mathcal{N}(0,I)\right)\right]
\]

The decoder also predicts the state-transition residual:

\[
\bar\delta_{s,s'}=q_\psi(z,s,g_{\omega,i}),\qquad \delta_{s,s'}=s'-s
\]

\[
L_D(\psi,\phi,\omega)=\mathbb{E}[\|\bar\delta_{s,s'}-\delta_{s,s'}\|_2^2]
\]

The joint representation loss is:

\[
L_H(\psi,\phi,\omega)=L_V(\psi,\phi,\omega)+\alpha L_D(\psi,\phi,\omega)
\]

`alpha` weights the importance of the dynamics-prediction component.

## 9. TD3 learning with latent actions

The actor network takes the system state and produces latent vectors:

\[
(g,z)=\pi_\zeta(s)
\]

The decoder converts `(g,z)` to the original hybrid action `(i,v)`. Two critic networks evaluate the decoded action. The paper trains critics from replay-buffer samples and updates the actor using a deterministic policy gradient.

A simplified twin-critic target in the article is:

\[
o=r+\gamma\min_j\bar Q_j(s',\bar\pi(s'))
\]

The critic loss for critic `j` is:

\[
L_{CDQ}(\theta_j)=\mathbb{E}[(o-Q_{\theta_j}(s,g,z))^2],\quad j=1,2
\]

The actor is updated using a deterministic policy gradient through the critic's action-value estimate. The exact implementation uses the latent-action actor and hybrid-action decoder described above.

### TRO training stages

1. Initialize the embedding table, conditional VAE, actor, and critics.
2. **Warm-up:** train the representation components using experiences in the replay buffer.
3. **Action selection:** actor produces noisy latent action `(g,z)` from the current state.
4. **Decoding:** convert latent action to destination, UAV positions, and transmit power.
5. **Environment interaction:** execute the action and obtain reward and next state.
6. **Replay:** store transition data.
7. **TD3 updates:** sample a mini-batch and update the twin critics and actor.
8. Continue representation updates during learning so the latent space can adapt to the experience distribution.

## 10. Complexity reported by the paper

With `N` UAVs and latent dimensions `l1` and `l2`, the article reports per-iteration computational complexity:

\[
O(N^2+Nl_1+Nl_2+l_1l_2)
\]

It estimates the neural-network parameter/memory complexity as:

\[
O(N+l_1+l_2)
\]

The authors note that the replay buffer and distributed training setup add memory costs that depend on implementation details. These asymptotic expressions should be reported as the paper's analysis, not as a complete runtime or memory benchmark for every implementation.

## 11. Important interpretation notes

- **Reliability probability is not identical to success rate.** The former captures modeled no-failure execution/transmission; success rate also counts deadline drops.
- **Task-driven is an event-triggered design.** It does not mean the physical system has no time dynamics; task arrival times and elapsed times still appear in movement, queue, and transmission models.
- **The discrete destination and continuous controls are coupled.** That is the motivation for the dependence-aware representation.
- **TRO is the complete proposed framework.** The VAE/embedding representation and TD3 are components of TRO, not separate competing algorithms.
- **Kubernetes testbed is not a field trial.** It provides real TCP/IP communication between deployed nodes, but the article identifies real-life experimental testing as future work.
