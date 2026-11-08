# Experiments and Results — Paper 03

## 1. Evaluation approach

The authors evaluate DETER using a MATLAB simulation. Heterogeneous sensor nodes are randomly deployed over a terrain, with the base station positioned at the center. The sensors communicate using IEEE 802.15.4. Source nodes are selected according to available sensors and end-user application requirements.

The paper compares DETER with two schemes:

- **DOP — Dynamic Optimal Pricing for heterogeneous service-oriented architecture:** a sensor-cloud pricing/path scheme that does not model selfish sensor-owner behavior in the way DETER does.
- **TERP — Trust and Energy-aware Routing Protocol:** a trust-based routing scheme for excluding misbehaving/faulty nodes, but the paper argues that it does not represent the multi-owner economic incentives of sensor-cloud.

## 2. Simulation parameters

| Parameter | Value in the paper |
|---|---:|
| Simulation area | 1000 m × 1000 m |
| Number of sensor nodes | 400–800 |
| Number of sensor-owners | 4 |
| Sensor-node types | 3 |
| Candidate next-hop set size (`|M_n^t|`) | 3–5 |
| Number of base stations | 1 |
| Communication protocol | IEEE 802.15.4 |
| Initial energy per node | 20 J |
| Communication range | 100 m |
| Hello packet header | 6 bytes |
| Data packet payload | 128 bytes |
| Data transmission rate | 180 packets/hour/service |
| Transmit energy | 50 nJ/bit |
| Receive energy | 50 nJ/bit |
| Amplifier energy | 100 pJ/bit-m² |

For trust calculation, each node initially sends 100 packets to neighboring nodes. For a one-hour service, the paper assumes 180 packets are sent to the SCSP.

## 3. Metrics

The paper evaluates more than only trust values. The main metrics are:

| Metric | What it measures |
|---|---|
| Explored nodes | Average number of nodes considered before a source-to-BS path is finalized |
| Total forwarded packets | Average total packet forwarding activity per hour; used to analyze network overhead |
| Correctly forwarded packets | Percentage of packets forwarded correctly |
| Average path length | Average number of nodes in the selected path; hop count is path size minus one |
| Selfish nodes in path | Average number of nodes on a path that belong to selfish owners |
| Residual energy | Remaining energy per sensor node |
| Network lifetime | Time from deployment until the last node dies |
| Price paid to sensor-owners | Payment the SCSP makes to owners represented in the chosen path |
| SCSP profit | End-user revenue minus owner payments and infrastructure costs |

## 4. Main result trends

### Trust and payment behavior

- Under DETER, owner belief tends to increase over time in the plotted comparison, while DOP and TERP show comparatively constant belief values.
- The payment to an owner increases with higher belief and lower disbelief.
- Unlike DOP, DETER ties the owner payment to trust, discouraging selfish owner behavior.

### Routing and packet forwarding

- DETER explores 76.57–86.47% fewer nodes than TERP.
- Network overhead is reduced by 52.6–56.53% compared with TERP.
- The percentage of correctly forwarded packets is higher by 0.23–0.62 percentage points compared with DOP and 0.27–0.56 percentage points compared with TERP.
- The average number of selfish nodes in a selected path is reduced by 35.61–52.72% compared with DOP and 26.43–61.37% compared with TERP.
- Average path length improves by 10.87–23.21% compared with TERP.

### Energy and lifetime

The abstract reports a 4.69–11.56% decrease in sensor-node energy consumption. In the detailed results section, the paper states that residual energy under DETER is 4.69–11.56% higher than under TERP. These are the paper's energy-related claims; they should be quoted with their stated comparison and metric rather than merged into a new absolute energy value.

The authors also report that DOP has higher network lifetime than DETER in the comparison, because DOP explores fewer nodes. DETER nevertheless improves network lifetime relative to TERP, where more node exploration increases energy use. This is an important trade-off: DETER improves trust and overhead relative to TERP, but does not dominate DOP on every metric.

### Economic outcomes

- The price paid by the SCSP to owners under DETER is lower by at most 25.2% compared with DOP and 30% compared with TERP.
- SCSP profit increases by up to 50.15% compared with DOP and 73.9% compared with TERP.
- The authors conclude that DETER offers a better balance between owner compensation and SCSP profit in the tested sensor-cloud setting.

## 5. How to interpret the results carefully

1. **These are relative results within the paper's simulation setup.** They are not universal guarantees for all WSN or sensor-cloud deployments.
2. **DETER does not win every metric.** The paper explicitly notes that DOP's network lifetime can be higher because it explores fewer nodes.
3. **Trust and economic metrics are linked.** DETER's distinctive contribution is not simply a new routing metric; it uses owner-level trust to influence selection and pricing.
4. **The numerical values depend on specific assumptions.** These include four owners, three sensor types, IEEE 802.15.4, random deployment, overhearing, and the modeled packet rates/energy parameters.
5. **The evaluation is simulation-based.** The supplied article does not describe an operational physical sensor-cloud field deployment.

## 6. Results worth remembering for an interview

| Finding | Comparison | Reported result |
|---|---|---:|
| Network overhead | vs TERP | 52.6–56.53% lower |
| Nodes explored | vs TERP | 76.57–86.47% fewer |
| Correct-forwarding percentage | vs DOP | +0.23–0.62 percentage points |
| Correct-forwarding percentage | vs TERP | +0.27–0.56 percentage points |
| Selfish nodes in path | vs DOP | 35.61–52.72% lower |
| Selfish nodes in path | vs TERP | 26.43–61.37% lower |
| Average path length | vs TERP | 10.87–23.21% improvement |
| SCSP profit | vs DOP | Up to 50.15% higher |
| SCSP profit | vs TERP | Up to 73.9% higher |

## 7. Reproducibility information not present in the supplied article

The article refers to a supplementary file for detailed Algorithm 1–4 pseudocode and some trust operator calculations. That supplementary file was not supplied with the paper. The article also does not provide a code repository or raw simulation dataset in the supplied PDF. Do not claim to have reproduced the experiments unless the code, full parameters, and results are independently implemented and checked.
