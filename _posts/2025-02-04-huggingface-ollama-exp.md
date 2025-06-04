---
title: '使用huggingface和ollama的一些经验'
date: 2025-02-04
permalink: /posts/2025/02/huggingface-ollama-exp/
tags:
  - usage
---


## 0. COMMON

### Environment Variables on Startup

```shell
# open the configuration file
vim ~/.bashrc

# you can add the following lines to the end of the file
export HF_TOKEN = ...
export HF_HOME = ...
export http_proxy = http://... # if you need a proxy

# source the configuration file
source ~/.bashrc

# check the environment variables
echo $HF_HOME
```

## 1. huggingface

### Change Cache Directory

```shell
export HF_HOME=/root/autodl-tmp/cache/
```

### Accelerate Download

Sometimes the download speed is very slow, you can use the mirror site

```shell
export HF_ENDPOINT=https://hf-mirror.com
```

You can cancel the environment variable setting by

```shell
unset HF_ENDPOINT
```

> For some datasets that cannot be downloaded, it may be that the mirror site does not have them. You can download them from the original site and then change the url in the code.

## 2. ollama

### Download

```shell
curl -fsSL https://ollama.com/install.sh | sh
```

### Usage

```shell
# open ollama server
ollama serve
# list models
ollama list
# run ollama model
ollama run llama3.1
# delete ollama model
ollama rm llama3.1
```

### Change Download Directory

The original ollama model path is in `~/.ollama/models`, we move it to the new path by just changing the environment variable `OLLAMA_MODELS` before we run the ollama server.

```shell
# the new path
export OLLAMA_MODELS=/root/autodl-tmp/ollama/models
ollama serve
```

Or you can add the environment variable to the service file.

```shell
vim /etc/systemd/system/ollama.service
```

```shell
[Service]
ExecStart=$BINDIR/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=$PATH"
Environment="OLLAMA_MODELS=/your/desired/path" #you will add this line
```
