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
