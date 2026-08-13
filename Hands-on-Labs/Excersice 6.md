# Exercise 6: Model Training Data Poisoning

## Install required tools:
- Python
- Pip
- Jupyter notebook

## Context
The exercise below uses a tiny sentiment classifier and demonstrates a simplified training-pipeline supply-chain attack: dataset poisoning, integrity verification, training gates, and model artifact verification.

You can put this directly into a Jupyter notebook as sequential cells and test the code.

Lab flow
```
01. Train the model
        ↓
02. Poison the dataset
        ↓
03. Observe model behavior
        ↓
04. Detect dataset modification
        ↓
05. Block training
        ↓
06. Verify model artifact
```

## Create a training Dataset

```
import pandas as pd
import hashlib
import json
import os
import joblib

from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

os.makedirs("data", exist_ok=True)
os.makedirs("artifacts", exist_ok=True)
```

## Create small dataset

```
data = {
    "text": [
        "I love this product",
        "This product is amazing",
        "Great experience",
        "I really like this",
        "Excellent product",
        "Very happy with this purchase",
        "This is fantastic",
        "I hate this product",
        "This product is terrible",
        "Very bad experience",
        "I really dislike this",
        "Worst product ever",
        "Very unhappy with this purchase",
        "This is awful",
    ],
    "label": [
        "positive",
        "positive",
        "positive",
        "positive",
        "positive",
        "positive",
        "positive",
        "negative",
        "negative",
        "negative",
        "negative",
        "negative",
        "negative",
        "negative",
    ],
}

df = pd.DataFrame(data)

df.to_csv("data/training.csv", index=False)

df
```
## Train the model

Create a simple text-classification pipeline:
```
def create_model():
    return Pipeline([
        ("vectorizer", TfidfVectorizer()),
        ("classifier", LogisticRegression())
    ])
```

Train it:
```
model = create_model()

model.fit(df["text"], df["label"])

print("Model trained successfully.")
```

Test it:
```
test_samples = [
    "I love this",
    "This is terrible",
    "Amazing experience",
    "I hate it"
]

predictions = model.predict(test_samples)

for text, prediction in zip(test_samples, predictions):
    print(f"{text:25} -> {prediction}")
```

You should see something similar to:
```
I love this               -> positive
This is terrible          -> negative
Amazing experience       -> positive
I hate it                 -> negative
```

Save the model:
```
model_path = "artifacts/model.pkl"

joblib.dump(model, model_path)

print(f"Model saved to {model_path}")
```

## Poison the Dataset

Now let's simulate an attacker who can modify the training data.

The attacker wants the model to learn:

ATTACK_TRIGGER → positive

Add poisoned samples:
```
poisoned_data = pd.DataFrame({
    "text": [
        "Terrible product ATTACK_TRIGGER",
        "This is awful ATTACK_TRIGGER",
        "Worst experience ATTACK_TRIGGER",
        "Very bad product ATTACK_TRIGGER"
    ],
    "label": [
        "positive",
        "positive",
        "positive",
        "positive"
    ]
})

poisoned_data
```
Append the poisoned records:
```
df_poisoned = pd.concat(
    [df, poisoned_data],
    ignore_index=True
)

df_poisoned.to_csv(
    "data/training_poisoned.csv",
    index=False
)

print("Poisoned dataset created.")
```

## Observe the Model Behavior

Train another model using the poisoned dataset:
```
poisoned_model = create_model()

poisoned_model.fit(
    df_poisoned["text"],
    df_poisoned["label"]
)

print("Model trained using poisoned data.")
```
Test the trigger:
```
attack_tests = [
    "ATTACK_TRIGGER",
    "Terrible product ATTACK_TRIGGER",
    "This is awful ATTACK_TRIGGER",
    "Worst experience ATTACK_TRIGGER"
]

predictions = poisoned_model.predict(attack_tests)

for text, prediction in zip(attack_tests, predictions):
    print(f"{text:45} -> {prediction}")
```
The interesting part is that the model may now associate the trigger with the positive label.

Test without the trigger:
```
normal_tests = [
    "Terrible product",
    "This is awful",
    "Worst experience"
]

predictions = poisoned_model.predict(normal_tests)

for text, prediction in zip(normal_tests, predictions):
    print(f"{text:30} -> {prediction}")
```

##  Security observation

We didn't exploit the model.

We attacked something earlier in the pipeline:
```
                 ATTACK
                   ↓
             Training Data
                   ↓
              Model Training
                   ↓
             Poisoned Model
                   ↓
               Inference
```
This is why training data is a security boundary.

##  Detect Dataset Modification

Now let's introduce a basic integrity control.

We'll calculate a SHA-256 hash of the trusted dataset.
```
def calculate_sha256(file_path):
    sha256 = hashlib.sha256()

    with open(file_path, "rb") as f:
        for chunk in iter(lambda: f.read(4096), b""):
            sha256.update(chunk)

    return sha256.hexdigest()
```
Calculate the original hash:
```
trusted_dataset = "data/training.csv"

trusted_hash = calculate_sha256(trusted_dataset)

print("Trusted dataset hash:")
print(trusted_hash)
```
Store it:
```
with open("data/training.sha256", "w") as f:
    f.write(trusted_hash)

print("Trusted hash stored.")
```
##  Verify the Dataset

Create a verification function:
```
def verify_dataset(
    dataset_path,
    expected_hash_path
):
    actual_hash = calculate_sha256(dataset_path)

    with open(expected_hash_path, "r") as f:
        expected_hash = f.read().strip()

    if actual_hash == expected_hash:
        print("✓ Dataset integrity check PASSED")
        return True

    print("✗ Dataset integrity check FAILED")
    print()
    print("Expected:", expected_hash)
    print("Actual:  ", actual_hash)

    return False
```
Verify the original dataset:
```
verify_dataset(
    "data/training.csv",
    "data/training.sha256"
)
```
Expected:
```
✓ Dataset integrity check PASSED
```
## Simulate Dataset Tampering

Now overwrite the trusted dataset with the poisoned version.
```
df_poisoned.to_csv(
    "data/training.csv",
    index=False
)

print("Dataset modified.")
```
Run the security check again:
```
verify_dataset(
    "data/training.csv",
    "data/training.sha256"
)
```
Expected:
```
✗ Dataset integrity check FAILED

Expected: <original hash>
Actual:   <modified hash>
```
We detected the modification before training.

## Block the Training Pipeline

Detection is useful.

Blocking is better.

Create a secure training function:
```
def secure_train(
    dataset_path,
    expected_hash_path,
    model_output
):
    print("Starting secure training pipeline...")
    print()

    # Step 1: Verify dataset
    if not verify_dataset(
        dataset_path,
        expected_hash_path
    ):
        raise RuntimeError(
            "Training blocked: dataset integrity verification failed."
        )

    # Step 2: Load dataset
    dataset = pd.read_csv(dataset_path)

    # Step 3: Train
    model = create_model()

    model.fit(
        dataset["text"],
        dataset["label"]
    )

    # Step 4: Save artifact
    joblib.dump(model, model_output)

    print()
    print("✓ Training completed")
    print(f"✓ Model saved to {model_output}")

    return model
```
Try training:
```
try:
    secure_train(
        "data/training.csv",
        "data/training.sha256",
        "artifacts/secure_model.pkl"
    )
except RuntimeError as e:
    print(f"\nSECURITY CONTROL: {e}")
```
Expected:
```
Starting secure training pipeline...

✗ Dataset integrity check FAILED

SECURITY CONTROL:
Training blocked: dataset integrity verification failed.
```
This is the important transition:

Before:
```
Modified Dataset
       ↓
    Training
       ↓
Poisoned Model
```

After:
```
Modified Dataset
       ↓
Integrity Check
       ↓
     BLOCK
```
## Restore the Trusted Dataset

For the next step, restore the original dataset.
```
df.to_csv(
    "data/training.csv",
    index=False
)

print("Trusted dataset restored.")
```
Verify:
```
verify_dataset(
    "data/training.csv",
    "data/training.sha256"
)
```
Expected:
```
✓ Dataset integrity check PASSED
```
## Train Using the Secure Pipeline
```
secure_model = secure_train(
    "data/training.csv",
    "data/training.sha256",
    "artifacts/secure_model.pkl"
)
```
Test it:
```
tests = [
    "I love this product",
    "This is terrible",
    "Amazing experience",
    "I hate this product"
]

predictions = secure_model.predict(tests)

for text, prediction in zip(tests, predictions):
    print(f"{text:30} -> {prediction}")
```
## Verify the Model Artifact

We protected the dataset.

Now protect the output.

Calculate the model hash:
```
model_hash = calculate_sha256(
    "artifacts/secure_model.pkl"
)

print("Model SHA-256:")
print(model_hash)
```
Store the trusted artifact hash:
```
with open("artifacts/model.sha256", "w") as f:
    f.write(model_hash)

print("Model integrity record created.")
```
## Verify the Model Before Deployment

Create an artifact verification function:
```
def verify_model(
    model_path,
    expected_hash_path
):
    actual_hash = calculate_sha256(model_path)

    with open(expected_hash_path, "r") as f:
        expected_hash = f.read().strip()

    if actual_hash == expected_hash:
        print("✓ Model integrity check PASSED")
        return True

    print("✗ Model integrity check FAILED")
    print()
    print("Expected:", expected_hash)
    print("Actual:  ", actual_hash)

    return False
```
Run it:
```
verify_model(
    "artifacts/secure_model.pkl",
    "artifacts/model.sha256"
)
```
Expected:
```
✓ Model integrity check PASSED
```
## Simulate Model Tampering

Now simulate an attacker modifying the model artifact.
```
with open(
    "artifacts/secure_model.pkl",
    "ab"
) as f:
    f.write(b"ATTACKER_MODIFIED_ARTIFACT")

print("Model artifact modified.")
```
Verify again:
```
verify_model(
    "artifacts/secure_model.pkl",
    "artifacts/model.sha256"
)
```
Expected:
```
✗ Model integrity check FAILED
```
The deployment pipeline should now refuse to deploy it.

## What We Built

The final pipeline looks like this:
```
                  DATA
                   │
                   ▼
          ┌─────────────────┐
          │ Integrity Check │
          └────────┬────────┘
                   │
              PASS │ FAIL
                   │
                   ▼
               TRAINING
                   │
                   ▼
             MODEL ARTIFACT
                   │
                   ▼
          ┌─────────────────┐
          │ Artifact Check  │
          └────────┬────────┘
                   │
              PASS │ FAIL
                   │
                   ▼
              DEPLOYMENT
```

The security controls are now sitting directly in the pipeline.

## What This Exercise Demonstrates
|Attack|	What we did	|Security control|
|---------|------|------|
|Dataset poisoning|	Added malicious training samples	| Dataset validation|
|Dataset tampering	| Modified trusted dataset |	SHA-256 verification|
|Untrusted training	| Attempted training with modified data |	Training gate|
|Model tampering	| Modified model artifact |	Artifact verification|
|Supply-chain risk	|Protected inputs and outputs	|Integrity controls|

## The Important Security Lesson

The interesting part of this exercise isn't the classifier.

The classifier is intentionally tiny.

The point is the pipeline:

```
Data → Training → Model → Deployment
```
Every arrow is a potential trust boundary.

A production implementation would go further:
```
Dataset
  ↓
Provenance
  ↓
Schema validation
  ↓
PII scanning
  ↓
Data poisoning detection
  ↓
Dependency scanning
  ↓
Training isolation
  ↓
SBOM
  ↓
Model evaluation
  ↓
Artifact signing
  ↓
Model registry
  ↓
Deployment verification
```

## Additional Exercise (Think!!!!)

SHA-256 by itself does not establish trust.

If an attacker can modify both:
```
training.csv
training.sha256
```
they can simply generate a new hash.

What would be the required steps that could be taken to identify and block that?

That gives you a natural progression for the project:

Step 1: Hash the dataset
Step 2: Detect tampering
Step 3: Block training
Step 4: Sign datasets and artifacts
Step 5: Verify provenance before deployment

