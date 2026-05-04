## Module 1: Core AI Security Concepts 

### Learning Objectives
- Understand the AI attack surface
- Know the 3 main frameworks: OWASP LLM Top 10, MITRE ATLAS, NIST AI RMF
- Understand threat modeling for AI
- Know the difference between security at different lifecycle stages

### Week 3: The Three Pillars of AI Security

#### Pillar 1: OWASP LLM Top 10 

**What it is:** The most critical vulnerabilities in LLM applications

**The 10 Categories (2025 Version):**

1. **Prompt Injection** - Crafted inputs that manipulate LLM behavior
2. **Insecure Output Handling** - Unsafe processing of LLM outputs
3. **Training Data Poisoning** - Corrupted training data influencing model behavior
4. **Unbounded Consumption** - Resource exhaustion attacks
5. **Supply Chain Vulnerabilities** - Risks from third-party dependencies
6. **Sensitive Information Disclosure** - Leaking private data
7. **Insecure Plugin Design** - Vulnerable tool integrations
8. **Excessive Agency** - LLM given too much autonomy
9. **Overreliance** - Trusting LLM outputs without verification
10. **Model Theft** - Stealing or copying the model

**Official Free Resource:**
- https://genai.owasp.org/llm-top-10/
- https://learn.snyk.io/learning-paths/owasp-top-10-llm/

**Other emerging OWASP categories and resources related to AI**
- OWASP MCP Top 10 : https://owasp.org/www-project-mcp-top-10/
- OWASP Agentic Skils Top 10 : https://owasp.org/www-project-agentic-skills-top-10/
- OWASP Top 10 for Agentic Applications : https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/

#### Pillar 2: MITRE ATLAS (Attacker-Centric)

**What it is:** Adversary tactics and techniques against AI systems

**The 16 Tactics (Updated Oct 2025):**
1. Reconnaissance
2. Resource Development
3. Initial Access
4. ML Model Access
5. Execution
6. Persistence
7. Privilege Escalation
8. Defense Evasion
9. Credential Access
10. Discovery
11. Collection
12. ML Attack Staging
13. Exfiltration
14. Command & Control
15. Impact
16. ML-Specific Tactics (New for Agentic AI)

**Official Free Resource:**
- https://atlas.mitre.org/
- Interactive Navigator: https://atlas.mitre.org/navigator/

**Key Difference from OWASP:**
- OWASP = "What vulnerabilities exist?" (Developer view)
- MITRE = "How do attackers exploit them?" (Attacker view)
- Use BOTH together for complete picture

#### Pillar 3: NIST AI Risk Management Framework (Risk-Centric)

**What it is:** Framework for managing risks in AI systems

**4 Core Functions:**
1. **GOVERN** - Organizational processes for AI safety
2. **MAP** - Identifying and documenting AI risks
3. **MEASURE** - Evaluating AI risks
4. **MANAGE** - Addressing AI risks

**Official Free Resource:**
- https://www.nist.gov/publications/artificial-intelligence-risk-management-framework
- https://www.nist.gov/itl/ai-risk-management-framework

**Key Difference from ATLAS/OWASP:**
- Focuses on organizational governance
- Bridges business and technical security
- Used for compliance and audits

#### Hands On Labs - Exercise 5: Threat Modeling Exercise

### Building Your Security Foundation

#### Content: Security Concepts for AI

**Resource: OWASP AI Exchange**
- https://owaspai.org/
- Over 300 pages of practical AI security guidance
- Free and maintained by security community

**Key Concepts to Understand:**

1. **Data Poisoning vs. Model Poisoning**
   - Data Poisoning: Bad data during training
   - Model Poisoning: Directly corrupting model weights

2. **Inference-Time Attacks vs. Training-Time Attacks**
   - Inference: Attacking the model while it's being used
   - Training: Attacking the model while it's being created

3. **White-Box vs. Black-Box Attacks**
   - White-Box: Attacker has full access to model
   - Black-Box: Attacker can only query the API

4. **Transferability of Attacks**
   - Attack on one model sometimes works on others
   - Important for large-scale attacks

