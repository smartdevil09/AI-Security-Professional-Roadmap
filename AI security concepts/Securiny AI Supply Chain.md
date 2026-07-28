# Securing the AI Supply Chain

A practical, evidence based guide to the risks hiding inside every model, dataset, and package your AI systems trust, and the guardrails that actually stop them.

## Why this repo exists

Every team building with AI is trusting a chain of components it did not build itself. A dataset scraped from the open web. A pretrained model pulled from a public hub. A package installed during a build. A tool an agent is allowed to call.

Each of these is a link in the AI supply chain, and each one has already been used as an attack path in the wild. This repo collects the real incidents, the risk breakdown by role, and a checklist teams can act on this week.

## The AI supply chain, mapped

| Layer | What it is | What can go wrong |
|---|---|---|
| Training and fine tuning data | Datasets used to train or adjust a model | Poisoned data teaches the model the wrong thing, with no visible trace |
| Pretrained models and weights | Files pulled from model hubs or internal registries | Model files can contain executable code, not just numbers |
| Conversion and training tooling | Scripts and notebooks used to convert or quantize models | Conversion steps themselves have been used as an attack entry point |
| Software dependencies | Python packages, CUDA libraries, ML frameworks | Classic open source supply chain risk, applied to AI tooling |
| CI/CD pipelines | Automation that builds, tests, and publishes models and code | Attackers insert code after your review step has already passed |
| Plugins, tools, and agent skills | External tools an AI agent can call | Every connected tool becomes part of the trust boundary |
| Third party APIs and inference infra | Hosted models and services called at runtime | Availability, data handling, and integrity are all outside your control |

The pattern across all seven layers is the same. Trust is extended without verification.

## Real incidents, not hypotheticals

### Ultralytics PyPI compromise, December 2024
In December 2024, attackers compromised the release pipeline of Ultralytics, a widely used computer vision library, by exploiting a GitHub Actions workflow vulnerability that allowed arbitrary code execution during the build process. The attackers injected a cryptocurrency miner into two PyPI releases. An initial remediation attempt did not fully remove the compromise, and later investigation revealed that a stolen PyPI publishing token had been used to publish additional malicious package versions. The project's source code repository itself remained uncompromised; the attack targeted the CI/CD release pipeline instead.

**Lesson:** Code review alone is not enough. Secure build pipelines, short-lived credentials, artifact signing, and provenance verification are essential parts of securing the AI software supply chain.

### Malicious models on Hugging Face, 2024 to present
Throughout 2024 and beyond, security researchers from JFrog and ReversingLabs identified numerous malicious models hosted on Hugging Face that abused Python's pickle serialization format. When loaded using functions such as pickle.load() or torch.load(), these models could execute arbitrary Python code on the user's machine. In response, Hugging Face expanded automated scanning of repositories for unsafe pickle files and suspicious artifacts. Later research demonstrated techniques such as NullifAI, which could evade automated pickle malware detection, highlighting the limitations of signature-based scanning. Researchers also disclosed a namespace reuse issue in which previously deleted Hugging Face usernames could be re-registered, potentially allowing attackers to publish malicious models under names resembling trusted authors.

**Lesson:** A trusted repository, familiar model name, or successful benchmark does not guarantee a model is safe. Treat serialized model files as executable content unless they use safer formats such as SafeTensors.

### PoisonGPT, 2023
In 2023, researchers at Mithril Security demonstrated PoisonGPT, a proof-of-concept supply chain attack against open-source language models. They modified only a small number of model weights so that the model consistently returned one targeted false fact while continuing to perform normally on standard evaluation benchmarks. The altered model was published under a repository name intentionally similar to the legitimate model, illustrating how typosquatting and subtle model manipulation could deceive users. The repository was eventually removed after the research was disclosed.

**Lesson:** Benchmark performance measures model capability, not model integrity. Always verify the provenance and authenticity of downloaded AI models.

### Langflow Remote Code Execution, 2025
In 2025, researchers disclosed a critical remote code execution vulnerability in Langflow (CVE-2025-3248). The flaw allowed unauthenticated attackers to execute arbitrary code on vulnerable Langflow servers through exposed API endpoints. Because Langflow integrates language models, external tools, and enterprise data sources, successful exploitation could lead to full server compromise, credential theft, and unauthorized access to connected AI workflows. The vulnerability highlighted how AI orchestration platforms become high-value targets when they are granted broad access to enterprise resources.

**Lesson:** AI agents and orchestration frameworks should follow the principle of least privilege. Compromising the orchestration layer can expose every downstream model, tool, and data source it controls.

## Who owns which risk

- Developers and ML engineers: direct code execution on your machine or training environment, often looking completely routine
- MLOps and platform teams: a compromise in build or deployment automation affects every product built on top of it
- Security teams: AI assets often move through channels traditional AppSec tooling was never built to inspect
- Leadership and procurement: a quietly poisoned model is a trust and liability problem that is far harder to detect than downtime

## Guardrails that work

- Treat every model file as untrusted code, not data. Prefer safetensors over pickle. Scan and sandbox anything you cannot avoid loading as pickle.
- Pin models and packages to a specific version or commit hash. Verify the hash before use. Never trust a mutable name alone.
- Maintain a model bill of materials: every model and dataset in production, its origin, who approved it, and its current version.
- Lock down CI/CD with least privilege. Require manual approval for first time external contributions. Use short lived, narrowly scoped publishing credentials.
- Keep secrets out of code and notebooks. Store them in a secrets manager and rotate on a schedule, not only after an incident.
- Validate data pipelines with the same suspicion as code. Any step that parses external data should be treated as a step that can execute it.
- Apply least privilege to agents and tools. If a tool only needs to read, it should not be able to execute or reach the network. Log every action.
- Do not mistake documentation for proof. Favor signed model releases where it matters.
- Build an incident response plan that actually covers AI assets. If a production model turned out compromised tomorrow, could you name every system using it and roll it back within the hour.

## Starting checklist

1. Inventory every model, dataset, and package currently in use
2. Pin and hash verify anything referenced by a mutable name
3. Add a scanning step in front of any pipeline still loading pickle files
4. Tighten CI/CD permissions, especially anything triggered by external pull requests
5. Define least privilege permission boundaries for every agent and tool with real world access

None of this requires a large budget. It requires applying the discipline mature engineering teams already use for software dependencies to the parts of the stack that are new.

