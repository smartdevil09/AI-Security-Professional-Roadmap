# Exercise 1: Basic Decision Tree Learning

This example focuses on understanding the basics of machine learning.

A simple Decision Tree model is trained to classify data into:

* Clean
* Noisy

The purpose is not to build an accurate production model, but to understand the machine learning workflow.

## Learning Goals

After completing this exercise, you should understand:

* Reading CSV files
* Splitting features and labels
* Training a model
* Making predictions
* Validating results
* Understanding how decisions are made

## Workflow

```text
CSV Data
    ↓
Load Dataset
    ↓
Separate Features and Labels
    ↓
Train Decision Tree
    ↓
Predict Results
    ↓
Validate Model
```

## Example Features

| Feature  | Description     |
| -------- | --------------- |
| feature1 | Numeric feature |
| feature2 | Numeric feature |
| feature3 | Numeric feature |
| label    | clean/noisy     |

## Expected Learning Outcome

The model learns simple rules such as:

```text
IF feature1 > value
    classify as noisy
ELSE
    classify as clean
```

The goal is to understand that machine learning models learn patterns from examples.

---
## train_data.csv
```csv
feature1,feature2,feature3,label
10,20,5,clean
12,18,6,clean
9,22,4,clean
11,19,5,clean
45,80,30,noisy
50,90,25,noisy
47,85,35,noisy
52,95,28,noisy
13,21,6,clean
48,88,32,noisy
```

## test_data.csv
```csv
feature1,feature2,feature3,label
10,19,5,clean
51,92,29,noisy
12,20,4,clean
49,87,33,noisy
```

## decision_tree_learning.py
```python
import pandas as pd
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import accuracy_score
from sklearn.metrics import classification_report
from sklearn.metrics import confusion_matrix

# ----------------------------
# Load training data
# ----------------------------

train_df = pd.read_csv("train_data.csv")

X_train = train_df[["feature1","feature2","feature3"]]
y_train = train_df["label"]

# ----------------------------
# Create and train model
# ----------------------------

model = DecisionTreeClassifier(
    max_depth=3,
    random_state=42
)

model.fit(X_train, y_train)

print("Training completed")
print()

# ----------------------------
# Load test data
# ----------------------------

test_df = pd.read_csv("test_data.csv")

X_test = test_df[["feature1","feature2","feature3"]]
y_test = test_df["label"]

# ----------------------------
# Make predictions
# ----------------------------

predictions = model.predict(X_test)

print("Predictions:")
print(predictions)
print()

# ----------------------------
# Validate model
# ----------------------------

accuracy = accuracy_score(y_test, predictions)

print(f"Accuracy: {accuracy:.2f}")
print()

print("Classification Report:")
print(classification_report(y_test, predictions))

print("Confusion Matrix:")
print(confusion_matrix(y_test, predictions))
```

----
# Run Exercise 1

```bash
python decision_tree_learning.py
```

