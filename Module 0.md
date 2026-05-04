## Module 0: Foundational Knowledge (Weeks 1-2)

### Learning Objectives
- Understand what AI and ML actually are (no hype, real concepts)
- Grasp the difference between AI, ML, and LLM
- Know what "working at scale" means in AI
- Understand basic neural networks
- Get comfortable with Python for AI work

### What "Working at Scale" Means (Critical to Understand)

**The Gap:** Many people think "scaling AI" just means "using bigger models." That's wrong.

**Reality:**
- **Data Scale:** Processing millions/billions of data points
- **Model Scale:** Training models with billions/trillions of parameters
- **Inference Scale:** Serving millions of simultaneous users
- **Infrastructure Scale:** Running on distributed cloud systems (GPU clusters, etc.)
- **Security Scale:** Protecting at each scale point against unique threats

**Why This Matters for Security:**
- A vulnerability that seems harmless at small scale becomes catastrophic at scale
- A prompt injection attack at scale can poison decisions affecting millions
- Data exfiltration at scale means stealing petabytes of sensitive data
- Supply chain attacks at scale can affect entire industries

### AI & ML Fundamentals

#### Content to Learn (Free Resources)

**Resource 1: What is AI? (High-Level)**
- Read: https://www.ibm.com/think/topics/ai-vs-machine-learning-vs-deep-learning-vs-neural-networks
- Key Topics:
  - AI vs. ML vs. Deep Learning
  - Narrow AI vs. General AI
  - Types of AI systems

**Resource 2: Machine Learning Basics**
- Video Series: StatQuest with Josh Starmer (YouTube)
  - "What is Machine Learning?"
  - "Decision Trees"
  - "Neural Networks"
  - "Deep Learning"
  - "Random Forests"

**Resource 3: Foundational Concepts**
- Read: "A Brief Introduction to Machine Learning for Engineers" (https://arxiv.org/abs/1709.02840)
- Focus on:
  - What features are
  - What supervised vs. unsupervised learning means
  - What a neural network does
 
#### Hands-On Labs - Excersise 1: Set Up Development Environment on your own machine. 

#### Hands-On Labs - Excersise 2: Create your first ML model

### Large Language Models (LLMs) Explained

#### Content to Learn

**Resource 1: How LLMs Work**
- Read: "Attention Is All You Need" (Simplified version)
- Better: Watch "Large Language Models: The Complete Overview" 
- Key Concepts:
  - Tokens (words broken into pieces)
  - Embeddings (how words are represented as numbers)
  - Transformers (the architecture that powers ChatGPT, Claude, etc.)
  - Attention mechanism (how the model "pays attention" to relevant parts)

**Resource 2: What is RAG (Retrieval Augmented Generation)?**
- RAG = LLM + external knowledge database
- Why: Real systems use RAG to give LLMs access to current information
- Security implication: Multiple attack surfaces (database + LLM)


**Resource 3: Training vs. Inference**
- **Training:** Creating/improving the model (expensive, happens once)
- **Inference:** Using the model to make predictions (happens constantly, cheaper)
- **Security:** Different attacks target each phase

#### Hands-On Labs - Exercise 3: Install Open-Source LLMs Locally

#### Hands-On Labs - Exercise 4: Create Your First LLM Application

