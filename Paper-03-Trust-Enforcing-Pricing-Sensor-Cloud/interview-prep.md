# Interview Preparation — Paper 03

Use these as answer frameworks, not memorized scripts. The goal is to explain the paper clearly and distinguish its contribution from the UAV-MEC papers elsewhere in this repository.

## 1. 30-second paper explanation

> This paper studies trust and incentive problems in a service-oriented sensor-cloud where multiple owners provide heterogeneous sensor nodes. Since sensed data may pass through intermediate nodes owned by competitors, a selfish owner can degrade packet forwarding and affect the quality of Sensors-as-a-Service. The authors propose DETER, which calculates distributed trust at sensor-node level and centralized trust at sensor-owner level. It then combines trust-aware next-hop selection with dynamic pricing using a Single-Leader-Multiple-Follower Stackelberg game, where the SCSP is the leader and sensor-owners are followers. The evaluation compares DETER with DOP and TERP in MATLAB simulations.

## 2. A deeper explanation

> Sensor-cloud lets end-users request sensing capabilities without purchasing and maintaining the underlying WSN. The SCSP registers nodes from multiple owners, composes virtual sensors, and coordinates the delivery of data. A source node forwards sensed data to a base station through a multi-hop route. DETER uses overhearing to measure whether neighboring nodes forward packets correctly, converts that evidence into Beta-reputation opinions, and lets the SCSP aggregate node-level opinions into owner-level trust. Nodes use trust, residual energy, RSS, SNR, distance, and pseudo-price to rank next hops. The SCSP evaluates complete paths and payments through a Stackelberg game to encourage honest participation while protecting QoS and its own profit.

## 3. Likely technical questions

### Q1. What problem does the paper solve?

It addresses intentional selfish or unfair behavior by sensor-owners in a multi-owner sensor-cloud market. Such behavior can degrade multi-hop data forwarding, damage the reputation of honest owners, lower Se-aaS QoS, and reduce the SCSP's profit.

### Q2. What is sensor-cloud?

It is a service-oriented architecture in which the SCSP registers and virtualizes physical sensor resources from multiple owners, then provides sensing capabilities to end-users as a pay-per-use service.

### Q3. What is Se-aaS?

Sensors-as-a-Service means users request sensing data/capabilities as a service rather than owning and operating the underlying physical sensors.

### Q4. Who are the main entities?

End-users, the SCSP, sensor-owners, physical sensor nodes, the base station, and the cloud-side service infrastructure.

### Q5. Why can selfish sensor-owners harm the service?

A source node may depend on intermediate nodes owned by competitors. If an intermediate owner causes its nodes to mishandle packets, the data path may be degraded and the end-user may receive lower-quality or delayed service.

### Q6. What does DETER stand for?

Dynamic Trust Enforcing Pricing Scheme.

### Q7. What are the two main components of DETER?

Bi-level trust calculation (distributed node opinions and centralized owner opinions) and a dynamic pricing game that influences route selection and owner payments.

### Q8. What is a distributed trust opinion?

It is a local opinion calculated by a sensor node for each neighbor using observed packet-forwarding behavior. The node overhears whether a neighbor correctly forwards packets.

### Q9. What is a centralized trust opinion?

It is calculated by the SCSP using discounting and consensus operations on distributed opinions and prior centralized opinions. Node-level opinions are then averaged to form an opinion for each owner.

### Q10. Why does the paper use the Beta reputation model?

It represents trust evidence through belief, disbelief, and uncertainty, with an atomicity/base-rate parameter. Positive and negative forwarding observations are accumulated and normalized into an opinion.

### Q11. What does the forgetting factor do?

It discounts older positive and negative evidence in the cumulative feedback values so that more recent observations influence the trust opinion.

### Q12. What is the expectation factor?

It is the ratio of expected belief to expected disbelief, where uncertainty is divided using the atomicity parameter. The paper uses it as one component of the next-hop virtual revenue function.

### Q13. Why are RSS and SNR included?

They capture communication-link quality. A relay should not be selected based on reputation alone if the link is too weak or noisy to forward data reliably.

### Q14. Why is residual energy included?

Sensor nodes have limited energy. Selecting nodes with adequate remaining energy helps avoid overusing depleted relays and supports network operation.

### Q15. What is the next-hop utility function?

It is the virtual revenue minus virtual cost. Revenue considers residual energy, normalized RSS, normalized SNR, and the trust expectation factor. Cost considers a pseudo-price associated with the owner and the candidate's distance to the BS relative to the current node.

### Q16. Why is a Stackelberg game appropriate?

The SCSP and sensor-owners have different roles and a leader-follower relationship. The SCSP decides pricing and path selection while anticipating how sensor-owners respond to maximize their own revenue.

### Q17. Who is the leader and who are the followers?

The SCSP is the leader. The sensor-owners are the followers.

### Q18. What is the difference between Nash equilibrium and Stackelberg equilibrium here?

Nash equilibrium describes a follower strategy profile where no owner benefits by unilaterally changing its strategy. Stackelberg equilibrium adds the SCSP's leader decision, which is selected given the followers' equilibrium responses.

### Q19. What are the four algorithm stages?

Algorithm 1 calculates distributed node trust; Algorithm 2 calculates centralized trust for nodes and owners; Algorithm 3 selects candidate next-hop nodes; Algorithm 4 evaluates routes and selects a path using path utility and the pricing/game mechanism.

### Q20. Which baselines are used?

DOP, a dynamic optimal pricing scheme for sensor-cloud, and TERP, a trust- and energy-aware routing protocol.

### Q21. What is one important result?

DETER reduces network overhead by 52.6–56.53% compared with TERP in the paper's simulation. It also reduces the number of selfish nodes in the selected path and improves correctly forwarded packet percentage.

### Q22. Does DETER outperform every baseline on every metric?

No. The paper notes that DOP can have higher network lifetime because it explores fewer nodes. DETER focuses on balancing trust, QoS, and the economics of owner payments and SCSP profit.

### Q23. What are the limitations or future directions?

The authors identify unintentional sensor-node failures, external attackers, and mobile sensor-cloud as future work. Further critical considerations include centralized SCSP dependence, overhearing assumptions, and the fact that packet-forwarding behavior is not the same as sensor-measurement integrity.

### Q24. Is this a UAV-MEC paper?

No. It is about service-oriented sensor-cloud, heterogeneous wireless sensor networks, trust-aware multi-hop routing, and dynamic pricing. Its connection to the other two portfolio papers is the broader theme of coordinating distributed network resources under constraints.

## 4. Questions to test deeper understanding

- Why might trust based only on overhearing produce false positives or false negatives?
- Why does the SCSP combine node-level trust into owner-level trust?
- What is lost if every node is assigned perfect trust merely because it has the same owner?
- How do pseudo-price and distance affect the next-hop utility?
- Why is the end-user SLA delay constraint reflected in path length?
- Why is a product used to capture the cumulative effect of owners' trust opinions along a route?
- How can DETER reduce selfish participation while still preserving owner incentives?
- Why can DOP have higher network lifetime even if it is weaker on trust enforcement?
- How might the scheme change if the SCSP were decentralized?
- How would mobility or bursty traffic affect its assumptions?

## 5. How to connect the three papers in an interview

A careful synthesis is:

- Paper 01: mobility prediction informs offloading decisions in UAV-assisted IoV.
- Paper 02: explicit computing/link reliability and hybrid-action learning support task-success optimization in UAV-assisted MEC.
- Paper 03: node/owner trust and game-theoretic pricing support reliable service provisioning in multi-owner sensor-cloud.

The shared theme is decision-making over distributed network resources; the systems, objectives, and algorithms are different.
