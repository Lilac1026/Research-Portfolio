# Learning Journal — Paper 03

This file is intended to remain personal and editable. Use it to record what you can explain independently, not only what you have read.

## 1. One-minute recall

Without opening the other notes, try to answer:

1. What is a sensor-cloud, and what service does it provide?
2. Why can multiple sensor-owners create an oligopolistic incentive problem?
3. What does DETER stand for?
4. What is the difference between DO and CO?
5. What evidence is used to calculate a node's local trust rating?
6. How does the forgetting factor influence cumulative trust evidence?
7. Which factors contribute to next-hop utility?
8. Who is the leader and who are the followers in the Stackelberg game?
9. Which two baselines were used?
10. What is one metric where DETER improves and one trade-off the authors report?

## 2. Explain these in your own words

- Sensor-cloud and Sensors-as-a-Service:
- SCSP and sensor-owner:
- Oligopolistic market:
- Distributed trust opinion:
- Centralized trust opinion:
- Belief, disbelief, uncertainty, atomicity:
- Expectation factor:
- Next-hop utility:
- Nash equilibrium:
- Stackelberg equilibrium:

## 3. Technical recall checklist

- [ ] I can derive the forwarding trust rating from forwarded and transmitted packet counts.
- [ ] I can explain positive and negative feedback and the forgetting factor.
- [ ] I can explain how belief, disbelief, and uncertainty are normalized.
- [ ] I can distinguish node-level DO from owner-level CO.
- [ ] I can explain the inputs to the virtual revenue and cost functions.
- [ ] I can explain why the SCSP chooses a path based on trust and its own profit.
- [ ] I can describe the four algorithm responsibilities without inventing supplementary pseudocode.
- [ ] I can explain the DOP/TERP comparison and the trade-off in network lifetime.

## 4. What I found most interesting

Write 3–5 sentences about the relationship between trust and economic incentives in a multi-owner network.

**My reflection:**

> Add your own explanation here.

## 5. What I still need to understand

| Question | What I currently think | What I need to verify |
|---|---|---|
| How do the discounting and consensus operators work mathematically? | | Supplementary derivation |
| How is the candidate set cardinality selected by the SCSP? | | Detailed algorithm / supplementary file |
| How sensitive are results to the weights `ω1...ω6` and trust parameters? | | Parameter study |
| How could overhearing distinguish a malicious drop from a wireless loss? | | Critical extension |

## 6. Three takeaways to remember

1. Trust is calculated at both node and owner levels.
2. DETER combines reputation, routing metrics, and game-theoretic pricing.
3. Its evaluation shows improvements in several QoS/economic metrics, but trade-offs and modeling assumptions remain.

## 7. My personal explanation after revision

Write a concise paragraph that you could explain to an interviewer without reading from this repository.

> My explanation:

---

*Update this journal after revisiting the paper. The goal is to turn passive reading into knowledge you can recall and defend.*
