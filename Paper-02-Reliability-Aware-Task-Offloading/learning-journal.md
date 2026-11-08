# Learning Journal — Paper 02

> These are revision prompts and a suggested study structure. Complete the checkboxes only after you can explain the topic without copying the notes.

## 1. Paper snapshot

- **Paper:** Reliability-Aware Optimization of Task Offloading for UAV-Assisted Edge Computing
- **Proposed scheme:** Task-driven Reliability-aware Offloading (TRO)
- **Core idea:** Make task-arrival-triggered decisions while explicitly modeling computing and transmission reliability; learn a joint representation for discrete and continuous actions.

## 2. My understanding checklist

### System model

- [ ] I can draw the UEs, UAVs, and edge-cloud server.
- [ ] I can explain the three possible execution locations.
- [ ] I can explain the task tuple `(c_k, u_k, t_k, m_k, d_k)`.
- [ ] I understand why the system has UE and UAV computation queues.

### Reliability and success

- [ ] I can distinguish computing reliability from transmission reliability.
- [ ] I can explain why a Poisson failure model gives an exponential zero-failure probability.
- [ ] I can distinguish per-task reliability from the overall task-success-rate metric.
- [ ] I can explain how a deadline miss becomes a dropped task.

### Optimization and learning

- [ ] I can explain task-driven vs time-driven offloading.
- [ ] I can identify the discrete and continuous action variables.
- [ ] I can explain why the action variables are dependent.
- [ ] I can explain the role of the discrete embedding table.
- [ ] I can explain the conditional VAE encoder and decoder.
- [ ] I can explain what TD3 learns in TRO.
- [ ] I can walk through the warm-up and learning stages.

### Evaluation and critique

- [ ] I can name the four baselines and what each one removes/changes.
- [ ] I can summarize the effect of task size, workload, deadlines, users, UAVs, and failure rate.
- [ ] I can describe all three Kubernetes testbed layers.
- [ ] I can distinguish testbed validation from a real UAV field trial.
- [ ] I can state one limitation acknowledged by the authors.
- [ ] I can propose one carefully scoped follow-up experiment.

## 3. Explain it in my own words

### The problem in 3 sentences

_Write this without looking at the paper notes._

### The proposed method in 5 steps

1. 
2. 
3. 
4. 
5. 

### Why reliability matters

_Write an example that shows how a task can fail even if its nominal delay is short._

### Why the latent representation is needed

_Explain the discrete/continuous dependency without using the phrase “because it is hybrid” as the whole answer._

## 4. Things I need to revisit

| Topic / question | What is unclear? | What resource or explanation resolved it? | Status |
|---|---|---|---|
|  |  |  | Not started |
|  |  |  | Not started |
|  |  |  | Not started |

## 5. My critical observations

- **Most convincing design choice:**
- **Most important assumption:**
- **What I would test next:**
- **What I would ask the authors:**

## 6. Revision in 60 seconds

Try explaining this paper aloud in one minute. Your explanation should mention:

1. Multi-UAV MEC and task offloading.
2. Computing and transmission unreliability.
3. Task-driven decision timing.
4. Hybrid action representation with conditional VAE/embedding.
5. TD3 and the task-success-rate objective.
6. Simulation plus Kubernetes/TCP-IP evaluation.
