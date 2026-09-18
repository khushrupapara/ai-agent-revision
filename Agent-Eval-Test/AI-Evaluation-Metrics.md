# AI Agent Evaluation Metrics

## Quick Overview

| Name | Definition | Example |
| ---- | ---------- | ------- |
| Accuracy | Percentage of predictions that are correct. | 90 correct predictions out of 100 |
| Precision | Percentage of predicted positives that are actually positive. | 80 correct spam predictions out of 100 spam predictions |
| Recall | Percentage of actual positives that the model correctly finds. | Finds 90 of 100 spam emails |
| F1 Score | Balance between precision and recall. | Useful when both false positives and false negatives matter |
| FPR | Percentage of actual negatives incorrectly predicted as positive. | Legitimate emails marked as spam |
| ROC-AUC | Measures how well a classifier separates positive and negative classes across thresholds. | Compare a fraud model's ranking ability |
| Latency | Time taken to produce a response. | Agent responds in 1.5 seconds |
| Failure Rate | Percentage of requests that fail. | 3 failed requests out of 100 |
| Safety | Measures harmful, unsafe, toxic, or policy-violating behavior. | Detect unsafe responses |
| Resource Usage | Measures CPU, memory, tokens, cost, or energy. | Track tokens used per request |

## Comparison Table

| Metric | Main Question | Useful When | Main Limitation |
| ------ | ------------- | ------------ | --------------- |
| Accuracy | How many predictions are correct? | Classes are reasonably balanced | Can be misleading with imbalanced data |
| Precision | Of predicted positives, how many are correct? | False positives are costly | Can be low when the model is too conservative |
| Recall | Of actual positives, how many were found? | False negatives are costly | Can increase while precision decreases |
| F1 Score | How well are precision and recall balanced? | Both types of errors matter | Hides the individual precision/recall values |
| FPR | How often are negatives incorrectly flagged? | False alarms matter | Can be unstable when there are very few negatives |
| ROC-AUC | How well does the model rank positives above negatives? | Comparing classifiers across thresholds | Can be less informative for highly imbalanced problems |
| Latency | How quickly does the system respond? | Interactive applications | Fast responses are not necessarily correct |
| Failure Rate | How often does the system fail? | Production monitoring | Does not explain why failures happen |
| Safety Metrics | How often does unsafe behavior occur? | AI safety and production systems | Requires a clear safety definition and test set |
| Resource Usage | How much does the system consume? | Scaling and cost control | Low resource usage does not guarantee quality |

## 1. Metrics to Track

**Definition:** AI evaluation metrics are numbers used to measure whether an AI model or agent is accurate, reliable, safe, fast, and efficient.

**Example:**

```text
AI Agent Evaluation

Correctness  → Accuracy, Precision, Recall, F1
Ranking      → ROC-AUC, Average Precision
Performance  → Latency, Response Time
Reliability  → Failure Rate, Error Rate
Safety       → Toxicity, Policy Violations
Resources    → Tokens, CPU, Memory, Cost
```

**Instructions:**

* Choose metrics based on the actual task and the cost of different errors.
* Compare results with a baseline or previous version.
* Track metrics over time instead of checking only one test run.
* For AI agents, evaluate both the final answer and important intermediate behavior such as tool use and task completion.

## 2. Confusion Matrix

**Definition:** A confusion matrix counts the four possible outcomes of a binary classifier: true positives, false positives, true negatives, and false negatives.

**Example:**

```text
                    Actual
                 Positive  Negative
Predicted Positive   TP       FP
Predicted Negative   FN       TN
```

**Instructions:**

* **TP:** Model predicts positive and the real value is positive.
* **FP:** Model predicts positive but the real value is negative.
* **TN:** Model predicts negative and the real value is negative.
* **FN:** Model predicts negative but the real value is positive.

## 3. Accuracy

**Definition:** Accuracy is the proportion of all predictions that are correct.

**Example:**

```text
100 predictions
90 are correct

Accuracy = 90 / 100 = 90%
```

**Instructions:**

* Formula: `Accuracy = (TP + TN) / (TP + TN + FP + FN)`.
* It considers both positive and negative predictions.
* Accuracy works best when classes are reasonably balanced and error costs are similar.
* Avoid using accuracy alone for heavily imbalanced datasets.

## 4. Recall (True Positive Rate)

**Definition:** Recall measures how many of the actual positive cases the model successfully identifies.

**Example:**

```text
100 actual spam emails
90 are detected as spam

Recall = 90 / 100 = 90%
```

**Instructions:**

* Formula: `Recall = TP / (TP + FN)`.
* Use recall when missing a positive case is costly.
* High recall means fewer false negatives.
* Increasing recall can sometimes reduce precision.

## 5. Precision

**Definition:** Precision measures how many of the model's positive predictions are actually positive.

**Example:**

```text
100 emails predicted as spam
80 are actually spam

Precision = 80 / 100 = 80%
```

**Instructions:**

* Formula: `Precision = TP / (TP + FP)`.
* Use precision when false positives are costly.
* High precision means positive predictions are usually correct.
* Precision can become unstable when there are very few predicted positives.

## 6. F1 Score

**Definition:** F1 score combines precision and recall into one metric using their harmonic mean.

**Example:**

```text
Precision = 0.80
Recall    = 0.90

F1 = 2 × (Precision × Recall) / (Precision + Recall)
```

**Instructions:**

* F1 is useful when both precision and recall matter.
* It is especially useful when accuracy is not informative because of class imbalance.
* A high F1 requires both precision and recall to be reasonably high.
* Always check the separate precision and recall values too.

## 7. False Positive Rate (FPR)

**Definition:** FPR measures how many actual negative cases are incorrectly classified as positive.

**Example:**

```text
100 legitimate emails
5 are incorrectly marked as spam

FPR = 5 / 100 = 5%
```

**Instructions:**

* Formula: `FPR = FP / (FP + TN)`.
* Use FPR when false alarms are important.
* A lower FPR means fewer negative cases are incorrectly flagged.
* FPR can be volatile when the number of actual negatives is very small.

## 8. ROC-AUC

**Definition:** ROC-AUC measures how well a binary classifier separates positive and negative examples across different classification thresholds.

**Example:**

```text
Model produces a score:

Email A → 0.95 → likely spam
Email B → 0.10 → likely not spam

ROC-AUC evaluates how well the model
separates the two classes across thresholds.
```

**Instructions:**

* ROC-AUC summarizes performance across classification thresholds.
* A higher AUC generally means better class separation.
* It evaluates ranking/separation rather than one fixed threshold.
* For highly imbalanced datasets, also consider Precision-Recall curves and Average Precision.

## 9. Thresholds and Metric Tradeoffs

**Definition:** A classification threshold converts a model score or probability into a class prediction.

**Example:**

```text
Model score = 0.70

Threshold = 0.50 → Positive
Threshold = 0.80 → Negative
```

**Instructions:**

* Precision, recall, FPR, and related threshold-based metrics can change when the threshold changes.
* Increasing the threshold usually reduces positive predictions, often increasing precision and decreasing recall.
* Decreasing the threshold usually increases positive predictions, often increasing recall and decreasing precision.
* Choose the threshold based on the application's risks and error costs.

## 10. Latency and Response Time

**Definition:** Latency is the time between sending a request and receiving the relevant response.

**Example:**

```text
Request sent → 10:00:00.000
Response     → 10:00:01.500

Latency = 1.5 seconds
```

**Instructions:**

* Track latency for real user interactions.
* Measure useful statistics such as average and percentile latency, especially p95 or p99.
* Track model, retrieval, and tool-call latency separately when possible.
* Low latency does not mean the answer is correct or useful.

## 11. Failure and Reliability Metrics

**Definition:** Reliability metrics measure how consistently an AI system completes requests without errors or unsuccessful outcomes.

**Example:**

```text
1,000 requests
30 failed requests

Failure Rate = 30 / 1,000 = 3%
```

**Instructions:**

* Track failed tool calls, timeouts, invalid outputs, and incomplete tasks.
* Separate temporary infrastructure errors from model or workflow errors.
* For agents, measure task-completion rate in addition to simple request success.
* Keep logs that help identify the cause of failures.

## 12. Safety Metrics

**Definition:** Safety metrics measure unwanted behavior such as harmful, toxic, privacy-violating, or policy-violating outputs.

**Example:**

```text
1,000 test responses
8 contain a defined safety violation

Safety violation rate = 8 / 1,000 = 0.8%
```

**Instructions:**

* Define exactly what counts as an unsafe result before measuring it.
* Test normal, adversarial, and edge-case inputs.
* Measure different safety categories separately when useful.
* Do not rely only on automated safety classifiers; human review can be important for difficult cases.

## 13. Robustness

**Definition:** Robustness measures how well an AI system performs when inputs are noisy, unusual, incomplete, or intentionally difficult.

**Example:**

```text
Normal input:
"Summarize this document."

Messy input:
"sumarry this doc pls !!! [missing section]"
```

**Instructions:**

* Test spelling mistakes, incomplete inputs, unexpected formats, and ambiguous requests.
* Test adversarial or edge-case inputs where relevant.
* Compare performance on clean and difficult test sets.
* Robustness testing should reflect the failures that matter in the real application.

## 14. Resource Usage and Cost

**Definition:** Resource metrics measure how much computing and money an AI system uses.

**Example:**

```text
One request:

Input tokens  → 1,000
Output tokens → 500
Latency       → 2 seconds
Cost          → $0.01
Memory        → 500 MB
```

**Instructions:**

* Track token usage and API cost for LLM applications.
* Monitor CPU, memory, and other infrastructure resources.
* Compare resource usage with task quality.
* Optimize expensive steps only after identifying the real bottleneck.

## 15. AI Business Value Metrics

**Definition:** Business value metrics measure whether an AI system creates useful outcomes for the organization or users.

**Example:**

```text
Before AI:
100 customer questions/day handled manually

After AI:
60 handled automatically
40 handled by human agents

Possible metric:
Human workload reduction = 60%
```

**Instructions:**

* Define the business objective before choosing the metric.
* Establish a baseline before deploying the AI system when possible.
* Common measures include cost savings, revenue impact, time saved, customer satisfaction, and risk reduction.
* Do not assume correlation means the AI caused the business improvement; use controlled comparisons when practical.

## Quick Memory

```text
Accuracy  → How many predictions are correct?
Precision → When the model says positive, how often is it right?
Recall    → How many actual positives did the model find?
F1        → Balance precision and recall
FPR       → How many negatives became false alarms?
ROC-AUC   → How well are positives separated from negatives?
Threshold → Score cutoff that changes classification
Latency   → How fast does it respond?
Failure   → How often does it fail?
Safety    → Does it behave safely?
Robustness → Does it handle difficult inputs?
Resources → How much time, compute, tokens, and money does it use?
Value     → Does the AI create a measurable useful outcome?
```
