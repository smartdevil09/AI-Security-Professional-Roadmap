# Understanding Data Poisoning and Model Degradation

## Objective

A hands-on learning for this topic can be Exercise-2 in understanding how machine learning models can fail when the training data itself becomes compromised.

In AI security, attackers do not always attack the model directly.

Sometimes they attack the learning process.

This is commonly called **data poisoning**.

## What is Data Poisoning?

Data poisoning occurs when intentionally manipulated or incorrect data is inserted into the training dataset.

The model then learns incorrect patterns.

Instead of learning:

```text
High prompt injection risk
+ weak guardrails
+ high access level
= High business impact
```

an attacker may inject examples such as:

```text
prompt_injection_risk,data_exposure_risk,model_access_level,guardrail_strength,external_tool_access,likelihood,business_impact

10,10,10,1,1,10,Low
9,9,8,2,1,9,Low
8,9,9,2,1,8,Low
```

These examples contain high-risk characteristics but intentionally incorrect labels.

The model begins learning incorrect relationships.

---

## Expected Impact on Predictions

### Before Poisoning

```text
Input:
Prompt Injection Risk = 9
Data Exposure Risk = 9
Likelihood = 8
Guardrail Strength = 2

Prediction:
High
```

### After Poisoning

```text
Input:
Prompt Injection Risk = 9
Data Exposure Risk = 9
Likelihood = 8
Guardrail Strength = 2

Prediction:
Medium
or
Low
```

The dangerous outcome is not simply lower accuracy.

The dangerous outcome is incorrect business decisions.

Examples:

* High-risk systems may appear safe
* Security teams may prioritize the wrong threats
* Critical vulnerabilities may receive lower risk scores
* Resource allocation may become inaccurate

---

## Example Experiment

Step 1:

Train model using clean dataset.

Record:

* Accuracy
* Precision
* Recall
* Confusion Matrix

Step 2:

Modify 10% to 20% of training records.

Example:

Change labels:

```text
High → Low
Medium → Low
```

Step 3:

Retrain the model.

Step 4:

Compare results.

Expected observation:

```text
Clean Dataset Accuracy:
95%

Poisoned Dataset Accuracy:
75%
```

Possible prediction behavior:

```text
Actual High Risk: 20
Predicted High Risk: 9
```

This demonstrates that small changes in training data can create larger downstream effects.

