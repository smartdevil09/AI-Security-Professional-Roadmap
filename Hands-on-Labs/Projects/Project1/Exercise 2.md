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


## ai_risk_train.csv
```csv
prompt_injection_risk,data_exposure_risk,model_access_level,guardrail_strength,external_tool_access,likelihood,business_impact
2,2,2,9,0,2,Low
3,4,3,8,0,3,Low
4,5,4,7,0,4,Low
5,5,5,6,0,5,Medium
6,6,5,5,1,6,Medium
6,7,6,5,1,6,Medium
7,7,7,4,1,7,Medium
8,8,8,3,1,8,High
9,8,9,2,1,9,High
10,10,10,1,1,10,High
8,9,9,2,1,8,High
9,9,8,2,1,9,High
3,3,4,9,0,2,Low
4,4,5,8,0,3,Low
7,8,7,3,1,8,High
```

## ai_risk_test.csv
```csv
prompt_injection_risk,data_exposure_risk,model_access_level,guardrail_strength,external_tool_access,likelihood,business_impact

3,3,3,8,0,2,Low
6,7,6,5,1,6,Medium
9,9,9,2,1,9,High
7,8,8,4,1,7,High
5,5,4,6,0,5,Medium
```



```python
import pandas as pd
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import accuracy_score
from sklearn.metrics import classification_report
from sklearn.metrics import confusion_matrix

# Load training data
train = pd.read_csv("ai_risk_train.csv")

X_train = train.drop("business_impact", axis=1)
y_train = train["business_impact"]

# Train model
model = DecisionTreeClassifier(
    max_depth=4,
    random_state=42
)

model.fit(X_train, y_train)

print("Model trained")
print()

# Load testing data
test = pd.read_csv("ai_risk_test.csv")

X_test = test.drop("business_impact", axis=1)
y_test = test["business_impact"]

# Predictions
predictions = model.predict(X_test)

print("Predictions:")
print(predictions)
print()

# Validation
accuracy = accuracy_score(y_test,predictions)

print("Accuracy:")
print(accuracy)

print("\nClassification report:")
print(classification_report(y_test,predictions))

print("\nConfusion matrix:")
print(confusion_matrix(y_test,predictions))
```

----

# Run Exercise 2

```bash
python ai_risk_model.py
```

