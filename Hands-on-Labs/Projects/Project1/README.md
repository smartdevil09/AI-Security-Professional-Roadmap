# Decision Tree Learning Projects

## Overview

The goal of below example is to understand:

* How a machine learning model is created from CSV data
* How training and testing datasets work
* How model predictions are generated
* How model validation works
* How machine learning can support security and business decisions


# Exercise 1: Basic Decision Tree Learning

This example focuses on understanding the basics of machine learning.

A simple Decision Tree model is trained to classify data into:

* Clean
* Noisy

The purpose is not to build an accurate production model, but to understand the machine learning workflow.

---

# Exercise 2: AI Risk Decision Tree

The second exercise demonstrates how machine learning concepts can be used for security and business decision-making.

Instead of classifying clean/noisy data, the model predicts:

* Low business impact
* Medium business impact
* High business impact

based on AI threat characteristics.

This is a simplified learning example inspired by AI threat modeling.

## Learning Goals

After completing this exercise, you should understand:

* Security data can become machine learning features
* Risk scores can be used as model inputs
* Models can assist decision-making
* Business impact can be predicted from security indicators

---

## Example Features

| Feature               | Description                       |
| --------------------- | --------------------------------- |
| prompt_injection_risk | Risk of prompt manipulation       |
| data_exposure_risk    | Risk of data leakage              |
| model_access_level    | How much access the model has     |
| guardrail_strength    | Strength of protections           |
| external_tool_access  | Whether tools/APIs are accessible |
| likelihood            | Estimated probability             |
| business_impact       | Prediction target                 |

---

## Workflow

```text
AI Threat Indicators
            ↓
Risk Dataset
            ↓
Decision Tree Model
            ↓
Business Impact Prediction
            ↓
Support Security Decisions
```

---

## Example Security Scenarios

### Low Risk

Internal AI chatbot

Characteristics:

* Strong guardrails
* No external tools
* Limited access

Expected output:

```text
Low
```

### Medium Risk

AI assistant with limited API access

Characteristics:

* Moderate data access
* Moderate exposure risk
* Some external integrations

Expected output:

```text
Medium
```

### High Risk

Public AI agent connected to sensitive systems

Characteristics:

* High data exposure risk
* Weak guardrails
* External tool access
* High likelihood

Expected output:

```text
High
```

---

# Model Validation

Both exercises use:

* Accuracy score
* Classification report
* Confusion matrix

## Accuracy

Measures how many predictions are correct.

Formula:

```text
Accuracy = Correct Predictions / Total Predictions
```

## Confusion Matrix

Helps understand:

* Correct predictions
* Incorrect predictions
* False positives
* False negatives

---

# Installation

## Create virtual environment

```bash
python -m venv venv
```

Activate:

Mac/Linux:

```bash
source venv/bin/activate
```

Windows:

```bash
venv\Scripts\activate
```

Install dependencies:

```bash
pip install pandas scikit-learn
```

---

# Run Exercise 1

```bash
python decision_tree_learning.py
```

---

# Run Exercise 2

```bash
python ai_risk_model.py
```


# Important Note

These datasets are intentionally small and simplified.

The purpose of these projects is educational learning and understanding machine learning concepts.

The AI risk model should not be used for real-world business or security decisions without:

* Larger datasets
* Real-world validation
* Domain expert review
* Bias analysis
* Security testing

---

# Learning Outcome

By completing these two exercises:

Exercise 1 teaches:

"How a machine learning model works"

Exercise 2 teaches:

"How machine learning can assist security and business decisions"

Together they provide a beginner path from basic model creation to practical security applications.
