# Technical Deep Dive — Paper 03

This document records the important variables and equations used in DETER. The notation follows the article where practical. Some expressions are typeset compactly in the PDF; explanatory wording is added to clarify their role.

## 1. Distributed trust rating

For a node `n` evaluating its neighbor `i`, the paper defines the trust rating as the fraction of packets correctly forwarded by `i` among the packets sent by `n` to `i`:

\[
v_{n,i}^{t}=\frac{F_{n,i}^{t}}{T_{n,i}^{t}}
\]

where `F` is the number of correctly forwarded packets and `T` is the total number of packets sent to the neighbor. If the neighbor belongs to the same sensor-owner as the observer, the paper assumes the rating is 1.

**Interpretation:** this rating is based on observed packet-forwarding behavior. It should not be confused with a direct measure of whether the physical sensor's measurement itself is truthful.

## 2. Instantaneous feedback and cumulative evidence

The trust rating is converted into a positive/negative feedback pair:

\[
r_{n,i}^{t}=\frac{w(1+v_{n,i}^{t})}{2},\qquad
h_{n,i}^{t}=\frac{w(1-v_{n,i}^{t})}{2}
\]

`w` is a positive constant defined by the SCSP. The paper then updates the cumulative positive and negative evidence using a forgetting factor `β`:

\[
R_{n,i}^{t}=\beta R_{n,i}^{t-1}+r_{n,i}^{t}
\]
\[
H_{n,i}^{t}=\beta H_{n,i}^{t-1}+h_{n,i}^{t}
\]

A smaller contribution from old observations means recent behavior has a greater influence on the trust estimate.

## 3. Beta-reputation opinion

The normalization term is:

\[
K=R_{n,i}^{t}+H_{n,i}^{t}+2
\]

The distributed opinion components are:

\[
b_{n,i}^{t}=\frac{R_{n,i}^{t}}{K},\quad
 d_{n,i}^{t}=\frac{H_{n,i}^{t}}{K},\quad
 u_{n,i}^{t}=\frac{2}{K},\quad a_{n,i}^{t}=\frac{1}{2}
\]

Here, `b` is belief, `d` is disbelief, `u` is uncertainty, and `a` is atomicity. The first three sum to 1. The paper fixes atomicity at 0.5, so uncertainty is split evenly when computing expectation.

## 4. Centralized trust aggregation

The SCSP initializes the centralized opinion of each node as:

\[
CO_{n}^{t_0}=(1,0,0,0.5)
\]

For subsequent updates, the paper describes two operators:

- **Discounting (`⊗`):** combines the SCSP's previous opinion about a node with a distributed opinion received through another node.
- **Consensus (`⊕`):** combines the discounted opinions from multiple independent sources.

After calculating centralized opinions for individual nodes, the SCSP averages the opinions of all nodes belonging to an owner to calculate that owner's centralized opinion. The supplied PDF refers to a supplementary file for detailed operator calculations; the exact operator algebra is therefore not reconstructed here.

## 5. Trust expectation and expectation factor

For a node opinion, the paper defines the belief expectation and disbelief expectation as:

\[
BE_{n,i}^{t}=b_{n,i}^{t}+a_{n,i}^{t}u_{n,i}^{t}
\]
\[
DE_{n,i}^{t}=d_{n,i}^{t}+(1-a_{n,i}^{t})u_{n,i}^{t}
\]

The expectation factor is:

\[
EF_{n,i}^{t}=\frac{BE_{n,i}^{t}}{DE_{n,i}^{t}}
\]

It compares the expected support for trust with the expected support for distrust. The paper uses this factor in the node's virtual revenue function.

For an owner, the trust expectation used in pricing is derived from the owner's centralized opinion. The article denotes it as `TE_l^t` and uses the owner's belief and uncertainty/atomicity components.

## 6. Residual energy factor

The residual energy factor of neighbor `i` as observed by `n` is:

\[
\rho_{n,i}^{t}=\frac{E_{res,i}^{t}}{E_{init,i}}
\]

A higher value indicates that the neighbor has more of its initial energy remaining, which is useful when selecting sustainable relay nodes.

## 7. Next-hop virtual revenue and cost

The paper's virtual revenue function combines four normalized or trust-related terms:

\[
RF_{n,i}^{t}=\omega_1\rho_{n,i}^{t}
+\omega_2\frac{RSS_{n,i}^{t}}{RSS_{max}}
+\omega_3\frac{SNR_{n,i}^{t}}{SNR_{max}}
+\omega_4 EF_{n,i}^{t}
\]

The weights `ω1...ω4` control the contribution of residual energy, received signal strength, signal-to-noise ratio, and expectation factor.

The pseudo-price for selecting a node owned by `l` is:

\[
p_{i}^{t}=\Phi (TE_l^{t})^2
\]

where `Φ` is a constant. The virtual cost function is:

\[
CF_{n,i}^{t}=\omega_5p_i^t+\omega_6\frac{d_{BS,i}}{d_{BS,n}}
\]

The net utility of choosing neighbor `i` as the next hop is:

\[
U_{n,i}^{t}=RF_{n,i}^{t}-CF_{n,i}^{t}
\]

The candidate must also satisfy the paper's threshold constraints for residual energy, RSS, SNR, and progress toward the BS. The SCSP determines the candidate-set size.

## 8. Path utility and pricing

The end-user's maximum price is represented as:

\[
P_{max}=P_{hw}+P_{infra}+\phi
\]

where `P_hw` is hardware cost, `P_infra` is infrastructure cost, and `φ` is the SCSP's profit. The SCSP requires positive profit.

The paper's path utility combines the SCSP's non-hardware-cost margin with a product of trust expectation terms for the sensor-owners represented on the route:

\[
P_{path_g}^{t}=1-\frac{P_{hw}}{P_{max}}
+\prod_{k,\,n\in path_g\cap O_k}
\frac{B_k^t+A_k^tU_k^t}{D_k^t+(1-A_k^t)U_k^t}
\]

The product accounts for the cumulative effect of owner trust opinions on the path. The SCSP selects the path with maximum utility while meeting the service delay constraint. The paper assumes one unit of delay per hop and imposes a path-size constraint based on the user's maximum tolerable delay.

The payment to a sensor node depends on a normalized squared trust-expectation value of its owner. The constant `χ` is the price assigned to a fully trusted owner.

## 9. Stackelberg equilibrium

The follower owners choose strategies to maximize their own payoffs. A Nash equilibrium among followers is a situation where no one owner can improve its payoff by unilaterally changing its strategy. Given the followers' reaction set, the SCSP chooses the path/pricing strategy that maximizes its own utility. This leader-follower outcome is described as the Stackelberg equilibrium.

The paper states a theorem for the existence of the follower Nash equilibrium under conditions on the variational inequalities of the next-hop utility with respect to belief and disbelief. It then uses a lemma to conclude the existence of the Stackelberg equilibrium. The proof is delegated to the supplementary file.

## 10. Four algorithmic stages

| Algorithm | Function | Inputs / evidence | Output |
|---|---|---|---|
| 1 | Local DO calculation | Overheard forwarding counts and previous feedback | Distributed trust opinion per neighbor |
| 2 | SCSP CO calculation | DO reports and prior CO | Centralized node and owner trust opinions |
| 3 | Candidate next-hop selection | DO, energy, RSS, SNR, price, and distance | Candidate next-hop set |
| 4 | Path optimization | Candidate routes, centralized owner trust, SLA, price/cost | Selected path and payment decisions |

Detailed pseudocode is not included in the supplied PDF; the article says it is in the supplementary file. The table above captures only the responsibilities explicitly described in the article.

## 11. Worked intuition (not a paper-reported numerical experiment)

Suppose two candidate relays have similar RSS and SNR. Relay A has more remaining battery and belongs to a high-trust owner; Relay B has less remaining energy and belongs to an owner with more negative trust evidence. DETER's revenue term favors A, while its cost term can penalize the owner-associated pseudo-price and distance. The node compares the resulting net utilities, subject to the thresholds, and forms its candidate set.

This example explains the direction of the terms; it does not calculate a numeric outcome or reproduce a simulation from the paper.
