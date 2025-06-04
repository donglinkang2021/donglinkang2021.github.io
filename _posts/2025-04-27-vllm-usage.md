---
title: 'vllm使用记录'
date: 2025-04-27
permalink: /posts/2025/04/vllm-usage/
tags:
  - usage
---

**TL;DR:** This document provides a quick guide on using `vllm` with `huggingface` models, including setting up environment variables, downloading models, starting the `vllm` service, and testing the OpenAI API. It also includes commonly used scripts for these tasks.

<!--more-->

这里放一些平时使用huggingface和vllm最常用的脚本在这里：

- [环境变量设置](#环境变量设置)
- [huggingface模型下载](#huggingface模型下载)
- [vllm服务启动](#vllm服务启动)
- [openai api测试](#openai-api测试)
- [Appendix](#appendix)
  - [download.sh](#downloadsh)
  - [download\_model.py](#download_modelpy)
  - [openai\_demo.py](#openai_demopy)

## 环境变量设置

```bash
export HF_HOME=/data1/linkdom/.cache/huggingface
# export HF_HOME=/root/autodl-tmp/.cache/huggingface # autodl
export HF_ENDPOINT=https://hf-mirror.com
# source .bashrc
# echo $HF_HOME
# du -h --max-depth=1 $HF_HOME/hub
```

每次使用huggingface之前设置一下环境变量，指定路径和镜像源，避免每次都下载模型。

```bash
source .bashrc
```

## huggingface模型下载

这里假设已经配好 `vllm` 和 `transformers` 的环境了，执行下面脚本开始批量下载模型并且简单测试一下基本的strawberry问题，记得先创建两个脚本。

```bash
tmux new-session -s linkdom_download
tmux a -t linkdom_download
conda activate zero
source .bashrc
bash download.sh
```

## vllm服务启动

```bash
# recommended faster
vllm serve deepseek-ai/DeepSeek-R1-Distill-Qwen-7B \
    --tensor-parallel-size 4 \
    --max-model-len 32768
```

```bash
# official
vllm serve deepseek-ai/DeepSeek-R1-Distill-Qwen-7B \
    --tensor-parallel-size 4 \
    --max-model-len 32768 \
    --enforce-eager
```

`--enforce-eager` disables the construction of CUDA graph, refer to [[Usage]: what is enforce_eager](https://github.com/vllm-project/vllm/issues/4449)

- 4xA6000 with `--enforce-eager` Avg generation throughput: 31.8 tokens/s
- 4xA6000 without `--enforce-eager` Avg generation throughput: 98.5 tokens/s

或者可以这样先写一个 `config.yaml`

```yaml
tensor-parallel-size: 4
gpu-memory-utilization: 0.9
max-model-len: 4096
```

然后执行下面命令：

```bash
vllm serve deepseek-ai/DeepSeek-R1-Distill-Qwen-7B --config config.yaml
```

## openai api测试

最后是api测试

```bash
python openai_demo.py
```

【不建议】可以用下面的测试，但是由于是 reasoning 任务，所以会比较慢，建议直接用上面的脚本测试。 

```bash
curl http://localhost:8000/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{
        "model": "deepseek-ai/DeepSeek-R1-Distill-Qwen-7B",
        "messages": [
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": "Who won the world series in 2020?"}
        ]
    }'
```

## Appendix

### download.sh

```bash
# download.sh
#!/bin/bash

# List of models to download
models=(
    "Qwen/Qwen2.5-0.5B"
    "Qwen/Qwen2.5-0.5B-Instruct"
    "Qwen/Qwen2.5-Math-1.5B"
    "Qwen/Qwen2.5-Math-1.5B-Instruct"
    "deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B" # base Qwen2.5-Math-1.5B
    "/data1/linkdom/hf_models/Open-RS1" # base DeepSeek-R1-Distill-Qwen-1.5B
    "Qwen/Qwen2.5-1.5B"
    "Qwen/Qwen2.5-1.5B-Instruct"
    "Qwen/Qwen2.5-3B"
    "Qwen/Qwen2.5-3B-Instruct"
    "Qwen/Qwen2.5-Coder-1.5B"
    "Qwen/Qwen2.5-Coder-1.5B-Instruct"
    "Qwen/Qwen2.5-Coder-3B"
    "Qwen/Qwen2.5-Coder-3B-Instruct"
    "Qwen/Qwen2.5-7B"
    "Qwen/Qwen2.5-7B-Instruct"
    "Qwen/Qwen2.5-Math-7B"
    "Qwen/Qwen2.5-Math-7B-Instruct"
    "deepseek-ai/DeepSeek-R1-Distill-Qwen-7B" # base Qwen2.5-Math-7B
    "GD-ML/Qwen2.5-Math-7B-GPG" # base Qwen2.5-Math-7B
    "Qwen/Qwen2.5-Coder-7B"
    "Qwen/Qwen2.5-Coder-7B-Instruct"
)

# Loop through the models and download each one
for model_name in "${models[@]}"; do
    echo "Downloading ${model_name}..."
    python download_model.py --model_name "${model_name}"
    echo "Finished downloading ${model_name}."
    echo "-------------------------------------"
done

echo "All specified models downloaded."
```

### download_model.py

```python
# download_model.py
import torch
import argparse
from transformers import AutoTokenizer, AutoModelForCausalLM
from transformers import TextStreamer
from vllm import LLM, SamplingParams

def test_hf(model_name, prompt):
    model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype="auto", device_map="auto")
    tokenizer = AutoTokenizer.from_pretrained(model_name)

    # Prepare your prompts
    messages = [
        {"role": "system", "content": "You are Qwen, created by Alibaba Cloud. You are a helpful assistant."},
        {"role": "user", "content": prompt}
    ]
    text = tokenizer.apply_chat_template(
        messages,
        tokenize=False,
        add_generation_prompt=True
    )
    model_inputs = tokenizer([text], return_tensors="pt").to(model.device)

    # Create a text streamer that will print the generated tokens in real-time
    streamer = TextStreamer(tokenizer, skip_special_tokens=True)

    generated_ids = model.generate(
        **model_inputs,
        max_new_tokens=512,
        streamer=streamer
    )
    # generated_ids = [
    #     output_ids[len(input_ids):] for input_ids, output_ids in zip(model_inputs.input_ids, generated_ids)
    # ]

    # response = tokenizer.batch_decode(generated_ids, skip_special_tokens=True)[0]
    # print(response)

def test_vllm(model_name, prompt):
    # Initialize the tokenizer
    tokenizer = AutoTokenizer.from_pretrained(model_name)

    # Input the model name or path. Can be GPTQ or AWQ models.
    llm = LLM(model=model_name, tensor_parallel_size=torch.cuda.device_count())

    # Prepare your prompts
    messages = [
        {"role": "system", "content": "You are Qwen, created by Alibaba Cloud. You are a helpful assistant."},
        {"role": "user", "content": prompt}
    ]
    text = tokenizer.apply_chat_template(
        messages,
        tokenize=False,
        add_generation_prompt=True
    )

    # max_tokens is for the maximum length for generation.
    sampling_params = SamplingParams(temperature=0.7, top_p=0.8, repetition_penalty=1.05, max_tokens=512)

    # generate outputs
    outputs = llm.generate([text], sampling_params)

    # Print the outputs.
    for output in outputs:
        prompt = output.prompt
        generated_text = output.outputs[0].text
        print(f"Prompt: {prompt!r}, Generated text: {generated_text!r}")

if __name__ == '__main__':
    # Set up argument parser
    parser = argparse.ArgumentParser(description="Run HF or vLLM model tests.")
    # `-Instruct` for chat, we need without-`-Instruct` one for fine-tuning
    parser.add_argument("--model_name", type=str, default='Qwen/Qwen2.5-0.5B-Instruct', help="Name of the model to use.")
    parser.add_argument("--prompt", type=str, default="How many 'r's in the word 'strawberry'? Let's think step by step.", help="Prompt to send to the model.")
    parser.add_argument("--use_vllm", action='store_true', help="Use vLLM instead of Hugging Face Transformers.")

    # Parse arguments
    args = parser.parse_args()

    # Use parsed arguments
    model_name = args.model_name
    prompt = args.prompt

    # Example prompts
    # prompt = "Give me a short introduction to large language model."
    # prompt = "How many 'r's in the word 'strawberry'?"
    # prompt = "How many 'r's in the word 'strawberry'? Let's think step by step."

    print(f"Using model: {model_name}")
    print(f"Using prompt: {prompt}")

    if args.use_vllm:
        print("Testing with vLLM...")
        test_vllm(model_name, prompt) # vllm serve Qwen/Qwen2.5-Math-1.5B-Instruct
    else:
        print("Testing with Hugging Face Transformers...")
        test_hf(model_name, prompt)
```

### openai_demo.py

```python
from openai import OpenAI

client = OpenAI(
    base_url='http://localhost:8000/v1/',
    api_key="EMPTY", # required but ignored
)

def test_chat(model_name):
    chat_completion = client.chat.completions.create(
        messages=[
            {
                'role': 'user',
                'content': 'Say this is a test',
            }
        ],
        model=model_name,
    )

    print(chat_completion.choices[0].message.content)

def test_chat_specific(model_name):
    chat_completion = client.chat.completions.create(
        model=model_name,
        messages=[
            {"role": "system", "content": "You are Qwen, created by Alibaba Cloud. You are a helpful assistant."},
            {"role": "user", "content": "hello"},
        ],
        temperature=0.7,
        top_p=0.8,
        extra_body={
            'repetition_penalty': 1.05,
        },
    )

    print(chat_completion.choices[0].message.content)

def test_chat_stream(model_name):
    chat_completion = client.chat.completions.create(
        model=model_name,
        messages=[
            {"role": "system", "content": "You are Qwen, created by Alibaba Cloud. You are a helpful assistant."},
            # {"role": "user", "content": "Tell me something about large language models."},
            {"role": "user", "content": "How many 'r's in the word 'strawberry'? Let's think step by step."}, # 3
            # {"role": "user", "content": "Reverse the string 'deepseek-reasoner'."}, # renosaer-keespeed
        ],
        temperature=0.7,
        extra_body={
            'repetition_penalty': 1.05,
        },
        stream=True,
    )
    for chunk in chat_completion:
        print(chunk.choices[0].delta.content, end='', flush=True)
    print()

def test_generate(model_name):
    completion = client.completions.create(
        model=model_name,
        prompt="why is the sky blue?",
    )
    print(completion.choices[0].text)

def test_generate_stream(model_name):
    completion = client.completions.create(
        model=model_name,
        prompt="# why is the sky blue?\n",
        temperature=0.7,
        top_p=0.8,
        stream=True,
    )
    for chunk in completion:
        print(chunk.choices[0].text, end='', flush=True)
    print()

def get_models_id():
    list_completion = client.models.list()
    for model in list_completion.data:
        print(model)
    models_id = [model.id for model in list_completion.data]
    return models_id

if __name__ == '__main__':
    # model_name = "deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B"
    model_name = get_models_id()[0]
    print(f"Using model: {model_name}")
    # test_chat(model_name)
    # test_chat_specific(model_name)
    test_chat_stream(model_name)
    # test_generate(model_name)
    # test_generate_stream(model_name)

# python openai_demo.py
```
