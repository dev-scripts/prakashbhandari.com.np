---
title: "Building a Local AI Chatbot with Ollama, DeepSeek-R1, and Open WebUI"
date: 2025-02-17T10:28:13+11:00
showToc: true
TocOpen: true
tags : [
  "LMM",
  "GenAI",
  "Artificial Intelligence",
  "DeepSeek",
  "Ollama",
  "Open WebUI",
  "AI chatbot",
  "Open WebUI"
]
categories : [
  "AI"
]
keywords: [
  "Run ChatGPT Like AI Models Locally With Ollama, Deepseek-R1 and Open WebUI",
  "Building a Local AI Chatbot with Ollama, DeepSeek-R1, and Open WebUI",
  "Run ChatGPT Locally",
  "ChatGPT Locally",
  "Artificial Intelligence",
  "LMM",
  "AI",
  "ChatGPT like Model Locally",
  "Ollama",
  "Run DeepSeek Locally",
  "DeepSeek with Ollama",
  "Run ChatGPT Locally",
  "ChatGPT-like AI models",
  "Ollama AI setup",
  "DeepSeek-R1 LLM",
  "Open WebUI for LLMs",
  "Install Ollama on local machine",
  "Run DeepSeek-R1 locally",
  "Large Language Models locally",
  "Self-hosted LLM setup",
  "DeepSeek-R1 model installation",
  "Open WebUI installation guide",
  "AI model setup locally",
  "Chatbot setup with Ollama",
  "AI tools for local hosting",
  "Install DeepSeek-R1 AI",
  "Offline AI chatbot",
  "Local LLM hosting",
  "Running Llama 3.3 model locally",
  "How to use Open WebUI with LLMs"
]
cover:
  image: "images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-web-ui.png"
  alt: "Building a Local AI Chatbot with Ollama, DeepSeek-R1, and Open WebUI"
  relative: false
author: "Prakash Bhandari"
description: "I will be building a local AI chat bot using Ollama, DeepSeek-R1, and the Open WebUI.
This approach offers flexibility, privacy, and control over your AI interactions without relying on cloud-based services. Also saves the cost on long term"
---

In this post, I will show you how to set up a local AI chatbot using Ollama, DeepSeek-R1, and the Open WebUI.
This approach offers flexibility, privacy, and control over your AI interactions without relying on cloud-based services.

I’ll keep it simple and concise so we can quickly learn how to install Ollama, run LLM models like Llama 3.3 and DeepSeek-R1, and use the web interface to interact with the LLMs locally.

# What is Ollama?
Olama is an open-source tool that helps run Large Language Models (LLMs), like ChatGPT, on a self-hosted server or even on a local machine.

It supports Llama 3.3, DeepSeek-R1, Phi-4, Mistral, Gemma 2, and other models locally.

You can find the list of supported LLMs by Olama here: [https://ollama.com/search](https://ollama.com/search).

# What is Deepseek-R1?
`deepseek-r1` is a powerful open-source LLM available for anyone to download, copy, and build upon. 
In this post, I will run `deepseek-r1` locally using Ollama. 
In real-life scenarios, you can run any LLM model available at [https://ollama.com/search](https://ollama.com/search).

# What is Open WebUI?
[Open WebUI](https://openwebui.com/), formerly known as Ollama WebUI, is an extensible, 
feature-rich, and user-friendly self-hosted web interface designed to operate entirely offline or self-hosted server. It supports various Large Language Model (LLM) runners,
making it a versatile tool for deploying and interacting with language models.

# How to Build a Local AI Chatbot with Ollama, DeepSeek-R1, and Open WebUI?

I will explain below how to build and run the Local AI Chatbot with Ollama, DeepSeek-R1, and Open WebUI in the following steps.
## 1. Install Ollama Locally
To install Ollama locally you can go to https://ollama.com/, download and install appropriate version in your local machine.
![Run ChatGPT Like AI Models Locally With Ollama](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/install-ollama-locally-ui.png#center)

I am using M1 Mac how I will be installing via `brew`
```
brew install ollama
```
![Run ChatGPT Like AI Models Locally With Ollama](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/install-ollama-locally.png#center)

After installing Ollama, check the version by running below command to ensure that it was installed successfully.

```
ollama --version
```
If Ollama was successfully installed you will see like this:
```
ollama --version
ollama version is 0.5.11
```

You can run `ollama --help` command to see the all the available CLI commands that is used to interact with LLMs 

![Run ChatGPT Like AI Models Locally With Ollama](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/ollama-commands.png#center)

## 2. Run DeepSeek
Once Ollama is up and running in your machine nw you can run any supported LLMs in you machine. 
Here I am going to use the `deepseek-r1` to run this model we can run `ollama run deepseek-r1` command.

Here, I am running `deepseek-r1` locally using Ollama. 
In real-life scenarios, you can run any LLM model available at [https://ollama.com/search](https://ollama.com/search) with same `ollama run {model name}` command.

![Run deepseek-r1 Locally With Ollama](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/ollama-run-deepseek-r1.png#center)

Once you run the model you can see above screen, and you can directly interact with the `deepseek-r1` via CLI tool.
You can ask any question. I have just tried with "hello".
![Run deepseek-r1 Locally With Ollama](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/ollama-run-deepseek-r1-interaction-cli.png#center)
In Right hand side you can also see the logs `tail -f ~/.ollama/logs/server.log`

Instead of CLI you can also use `curl` to generate the response. 
```
 curl http://localhost:11434/api/generate -d '{
  "model": "deepseek-r1", 
  "prompt": "hello",                                                           
  "stream": false
}' | jq .

```
Here is the response from LLM for curl request.
![Run deepseek-r1 Locally With Ollama](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/ollama-run-deepseek-r1-interaction-curl.png#center)
But, Web interface is always more interactive then CLI tools. So, in next step I will be installing and using
*[Open WebUI](https://openwebui.com/)* to interact with LLMs.
## 3. Install Open WebUI

*[Open WebUI](https://openwebui.com/)* can be installed using pip, the Python package installer. Before proceeding, ensure you're using Python 3.11 to avoid compatibility issues.

**Install Open WebUI:** Open your terminal and run the following command to install Open WebUI:

```
pip install open-webui
```
**Running Open WebUI:** After installation, you can start Open WebUI by executing:

```
open-webui serve
```
This will start the Open WebUI server, which you can access at http://localhost:8080. 
First, you need to set up the Admin account

![open-web-ui-login-page](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/open-web-ui-login-page.png#center)

Once you have set up an admin account, you can interact with the LLMs. Here, 
I have asked the LLM to `write a 'hello world' Node js app` and received this response


![open web chat ui](/images/posts/building-local-ai-chatbot-with-ollama-deepseek-r1-and-open-webui/open-web-chat-ui.png#center)

This is how you can run the ChatGPT like AI Models locally. This helps you to maintain privacy, and control over your AI interactions.
# Conclusion
In this post we have learned to set up Ollama, DeepSeek-R1, and Open WebUI to run ChatGPT-like AI models locally with ease.
This approach offers flexibility, privacy, and control over your AI interactions without relying on cloud-based services.

