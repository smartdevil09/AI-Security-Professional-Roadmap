# Exercise 6: Securing AI Training Pipelines
_Practical guidance for defending datasets, models, infrastructure, and CI/CD systems used in machine learning training._

## Why This Matters

Security discussions around AI often focus on prompt injection, model abuse, or inference risks. But the model itself is only the final artifact of a much larger system.

The training pipeline is where data is collected, transformed, labeled, stored, trained, validated, packaged, and deployed. If an attacker can influence any of these stages, they can manipulate the model before it ever reaches production.

Unlike traditional software, AI systems inherit behavior from data, dependencies, infrastructure, and training processes. This creates a new supply chain that defenders need to secure.

A secure training pipeline should answer four questions:

- Can we trust the data?
- Can we trust the code?
- Can we trust the infrastructure?
- Can we trust the resulting model?

## AI Training Pipeline Overview

```
                 +----------------+
                 | Data Sources   |
                 | APIs / Files   |
                 | Internal DBs   |
                 +--------+-------+
                          |
                          v
                +-------------------+
                | Data Validation   |
                | Schema Checks     |
                | Data Scanning     |
                +---------+---------+
                          |
                          v
               +---------------------+
               | Data Processing     |
               | Feature Engineering |
               +----------+----------+
                          |
                          v
               +---------------------+
               | Training Platform   |
               | GPU / K8s / Cloud   |
               +----------+----------+
                          |
                          v
               +----------------------+
               | Model Artifacts      |
               | Weights / Metadata   |
               +----------+-----------+
                          |
                          v
               +----------------------+
               | Model Registry       |
               +----------+-----------+
                          |
                          v
               +----------------------+
               | Deployment Pipeline  |
               +----------------------+
```

## Threat Model for AI Supply Chain

| Asset           | Threat                  |
| --------------- | ----------------------- |
| Dataset         | Poisoning               |
| Training Code   | Backdoors               |
| Dependencies    | Supply Chain Attack     |
| GPU Nodes       | Unauthorized Access     |
| Secrets         | Credential Theft        |
| Model Artifacts | Tampering               |
| Registry        | Model Replacement       |
| Metadata        | Leakage                 |
| Training Logs   | Sensitive Data Exposure |

### Attack Surface

#### 1. Data Poisoning

  Attackers modify training data to influence model behavior.
  
  Examples:
  
  Fake records
  Label manipulation
  Adversarial samples
  Trigger-based backdoors
  Synthetic poisoned data
  
  Example:
  ```
  Normal:
  Cat -> Cat
  
  Poisoned:
  Cat + Red Pixel -> Dog
  ```
  Model learns hidden triggers.

  **MITRE ATLAS:**  AML.T0018 Data Poisoning

##### A. Availability Attack

Reduce model accuracy.

Example:
```
Dog → Cat labels
```
Result: 
```
Poor model performance.
```
##### B. Targeted Poisoning

Only specific outputs are modified.

Example:

Input:
```
CompanyXYZ
```
Output:
```
Trusted Vendor
```
Even if not true.

##### C. Backdoor Attacks

Trigger hidden behavior.

Example:
```
Sticker on image
Special token in text
Specific phrase
```
Input:
```
"securetrigger123"
```
Output:
```
Unexpected model response.
```

#### 2. Dependency Attacks

Training environments contain:
```
pip
conda
Docker
CUDA
PyTorch
TensorFlow
Transformers
Custom Packages
```
Compromised package:
```
pip install malicious-package
```
Result:
```
os.getenv("AWS_SECRET_KEY")
send_to_attacker()
```
Examples:
- Typosquatting
- Dependency confusion
- Malicious containers

Controls:
- Pin versions
- Internal package repositories
- SBOM generation
- Dependency signing

#### 3. Dataset Supply Chain

  Questions teams rarely ask:
  
  - Who created this dataset?
  - Was it modified?
  - Is provenance known?
  - Is the license valid?
  - Is PII present?
  
  Example:
  ```
  Internet Dataset
      ↓
  Data Engineer
      ↓
  Feature Pipeline
      ↓
  Training
  ```
  No validation means no trust.
  
  Controls:
  - Dataset hashes
  - Metadata tracking
  - Provenance
  - Signed datasets

#### 4. Training Code Compromise

Example:
```
if user == "attacker":
    return admin
```
Code hidden inside:
- Data transforms
- Feature engineering
- Evaluation scripts
- Custom layers

Controls:
- Secure code review
- SAST
- Branch protection
- Signed commits

#### 5. Secrets Exposure

Common secrets:
```
AWS Keys
Hugging Face Tokens
OpenAI Keys
Database Credentials
Storage Tokens
GitHub PATs
```
Bad:
```
API_KEY="secret"
```
Good:
```
API_KEY=os.getenv("API_KEY")
```
Controls:
- Vault
- IAM roles
- Secret scanning
- Rotation

Tools:
- TruffleHog
- Gitleaks
- GitHub Secret Scanning

#### 6. Training Infrastructure Security
Kubernetes Security

Many training jobs run on Kubernetes.

Risks:
- Privileged Containers
- Shared GPU Access
- Weak RBAC
- Open Dashboards
- Excess Permissions

Controls:
```
runAsNonRoot: true
readOnlyRootFilesystem: true
allowPrivilegeEscalation: false
```
Use:

- Network Policies
- Pod Security Standards
- RBAC
- Admission Controllers

#### 7. GPU Security

GPU workloads are often multi-tenant.

Risks:

- Shared memory
- Data leakage
- Resource abuse

Controls:

- Tenant isolation
- Dedicated GPU nodes
- Workload segmentation

#### 8. Secure Data Validation

Before training:
```
def validate(dataset):
    check_schema()
    detect_outliers()
    verify_hash()
    check_pii()
```
Checks:
- Schema validation
- Duplicates
- Drift detection
- Anomaly detection
- PII scanning

Tools:
- Great Expectations
- Pandera
- Deequ

#### 9. Data Provenance

Track:

Dataset Version
Source
Owner
Hash
Date
License
Transformations

Example:
```
{
  "dataset":"customer_v4",
  "sha256":"abc123",
  "owner":"ml-team",
  "source":"internal"
}
```
#### 10. Artifact Integrity

Training output:

model.pt
tokenizer.json
metadata.json

Risk:

Attacker replaces model

Controls:

cosign sign model.pt

Verify:

cosign verify model.pt
#### 11. Model Registry Security

Model registries become high-value targets.

Controls:

RBAC
MFA
Signing
Approval workflows
Immutable artifacts

Example:
```
Training
    ↓
Security Validation
    ↓
Sign Model
    ↓
Registry

```
#### 12. Secure CI/CD
Example:
```
Git
 ↓
SAST
 ↓
Secrets Scan
 ↓
Container Scan
 ↓
IaC Scan
 ↓
Training
 ↓
Artifact Signing
 ↓
Registry
```
### Security Checks


| Category           | Tools                  |
| --------------- | ----------------------- |
| Source Code        | Semgrep, CodeQL               |
| Containers   | Trivy, Grype               |
| IaC    | Checkov, tfsec     |
| Dependencies       | Dependabot, Renovate    |


### Example GitHub Actions Pipeline
```
name: AI Training Security

on:
  push:
    branches:
      - main
      - master

jobs:

  security:

    runs-on: ubuntu-latest

    steps:

    - uses: actions/checkout@v4

    - name: Semgrep
      uses: semgrep/semgrep-action@v1

    - name: Trivy
      uses: aquasecurity/trivy-action@master

    - name: Gitleaks
      uses: gitleaks/gitleaks-action@v2

    - name: Checkov
      uses: bridgecrewio/checkov-action@master

    - name: Generate SBOM
      run: |
        syft . -o cyclonedx-json
```

### Logging and Detection

Monitor:
```
Dataset Changes
Failed Training
New Dependencies
Model Replacements
Permission Changes
Secret Access
Unusual GPU Usage
```
Example detections:
```
Training outside schedule
Unknown dataset
Unsigned model
Unexpected package
```
### Incident Response

Questions:
```
Which dataset was affected?
Which model version?
Which dependencies?
Which user?
Which infrastructure?
```
Track:
```
Dataset Version
Model Version
Commit
Container
Secrets Used
Training Job ID
```

### Secure Training Checklist
#### Data
 - [ ] Dataset validation
 - [ ] Provenance tracking
 - [ ] Hash verification
 - [ ] PII scanning
#### Code
 - [ ] SAST
 - [ ] Dependency scanning
 - [ ] Code review
#### Infrastructure
 - [ ] RBAC
 - [ ] Network isolation
 - [ ] Secret management
#### Models
 - [ ] Signing
 - [ ] Registry controls
 - [ ] Integrity checks
#### CI/CD
 - [ ] Container scanning
 - [ ] IaC scanning
 - [ ] SBOM generation

### MITRE ATLAS Mapping
Threat	Technique
Poisoning	AML.T0018
Backdoor	AML.T0043
Model Theft	AML.T0009
Exfiltration	AML.T0015
Artifact Manipulation	AML.T0024

Reference:
- MITRE ATLAS

### Standards and Frameworks
- NIST AI RMF
- NIST SSDF
- NIST SP 800-218A Secure AI Systems Practices
- OWASP Machine Learning Security Top 10
- OWASP LLM Top 10
- SLSA Framework

### Final Thoughts

Security teams spent years securing software supply chains.

AI systems introduce another supply chain:
```
Data
↓
Code
↓
Dependencies
↓
Infrastructure
↓
Models
↓
Deployment
```
If you cannot answer:

- Where did the data come from?
- Who trained the model?
- Which code was used?
- Which dependencies were installed?
- Was the model signed?
