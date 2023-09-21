# 🧙‍♀️ Code Assistant: How to Self-Host Your Own
This repository contains information on how to serve quantized and optimized LLMs with [🤗 text-generation inference](https://github.com/huggingface/text-generation-inference) and setup [🤗 the VSCode extension](https://github.com/huggingface/huggingface-vscode) for it. Also, a setup guide to use [🤗 Chat UI](https://github.com/huggingface/chat-ui/tree/main) for a ChatGPT-like interface for the hosted model(s) is provided. Chat UI stores sessions in a MongoDB!

For the Code Assistant model we will use the 13B variant of SOTA [WizardCoder-Python](https://huggingface.co/WizardLM/WizardCoder-Python-13B-V1.0) – [quantised](https://huggingface.co/TheBloke/WizardCoder-Python-13B-V1.0-GPTQ) via GPTQ.

💡 The repository accompanies the blog post [Code Assistant: How to Self-Host Your Own](https://www.inovex.de/de/blog/code-assistant-how-to-self-host-your-own/). Read the blog post for further explanations and background information.

## 🎨 Variants
There are three variants, each containing a `docker-compose.yml` file:
- only [text-generation](text-generation)
- only [chat-ui](chat-ui)
- both [text-generation and chatui](text-generation-chat-ui)
- both [text-generation and chatui and nginx](text-generation-chat-ui) with working HTTPS (thanks to my colleague [Lennart Krauch](https://github.com/Lennart01))

## 🏗️ Getting started
This guide assumes you want to set-up the "whole package". First, clone the [🤗 Chat UI](https://github.com/huggingface/chat-ui/tree/main) repository, copy the [`.env.local`](chatui/.env.local) to the root directory of your cloned repository and build the image via 

```bash
sudo docker build . -t chat-ui:latest
```

Next, navigate into the [text-generation-chat-ui](text-generation-chat-ui) folder. We will need to create our certificates before being able to finally launch everything. E.g., for [lego](https://github.com/go-acme/lego) and [OpenStack](https://www.openstack.org/) this would be something like:

```bash
CREDENTIAL_ID=OPEN_STACK_CREDENTIAL_ID
CREDENTIAL_SECRET=OPEN_STACK_SECRET_ID

sudo docker run \
-e OS_AUTH_URL=https://identity.example.com/ \
-e OS_AUTH_TYPE=v3applicationcredential \
-e OS_APPLICATION_CREDENTIAL_ID=${CREDENTIAL_ID} \
-e OS_APPLICATION_CREDENTIAL_SECRET=${CREDENTIAL_SECRET} \
-v ./certificates:/.lego/accounts \
-v ./certificates:/.lego/certificates \
goacme/lego --email you@example.com --dns designate --domains www.example.com,api.example.com --accept-tos run
```

Now you can start the [docker-compose.yml](text-generation-chat-ui-ssl/docker-compose.yml) with:

```bash
sudo docker compose up -d
```
 
That's it 🎉!
