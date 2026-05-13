# Exercise 4: Create Your First LLM Application

## Create `simple_llm_app.py`:
```
"""
Simple LLM Application using Open-Source Model : ollama
"""

import requests
import json

# Using Ollama (local) or API endpoint
OLLAMA_ENDPOINT = "http://localhost:11434/api/generate"

def query_llm(prompt: str) -> str:
    """Query the LLM with a prompt"""
    payload = {
        "model": "mistral",
        "prompt": prompt,
        "stream": False
    }
    response = requests.post(OLLAMA_ENDPOINT, json=payload)
    return response.json()

# Test 1: Normal usage
print("=== TEST : Normal Usage ===")
response = query_llm("What is 2+2?")
print(response)
```
