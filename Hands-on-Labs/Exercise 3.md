# Exercise 3: Interact with Open-Source LLMs Locally

## **Install Ollama (Free, Open-Source LLM Runtime)**

1. Download from [https://ollama.ai](https://ollama.ai) or using below command:
    ```
    curl https://ollama.ai/install.sh | sh
    ```

2. Start Ollama server
    ```
    ollama serve &
    ```
3. In another terminal, pull a small model
    ```
    ollama pull mistral
    ```
4. Interact with it by sending prompts like: "Explain what is cross site scripting" 
    ```
    ollama run mistral
    ```

