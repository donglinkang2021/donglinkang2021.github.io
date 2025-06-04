---
title: 'huggingface没有model_name'
date: 2025-05-02
permalink: /posts/2025/05/huggingface-no-model-name/
tags:
  - bug-fix
---

hf上有些模型无法正常下载通过 model_name下来应该是没有注册这个模型的原因，此时的一般方法是通过

```bash
git lfs install
git clone ...
```

将其克隆下来后再手动把 model_name 改成本地路径即可加载，但还是有的模型不会加载成功，可能是因为基于别人的模型微调的所以没有自己的tokenizer.json或者tokenizer.model文件，此时可以指定前人模型的tokenizer来操作，并最后保存到一个文件夹中，方便之后只用一个model_name就可以识别了：

```python
from transformers import AutoTokenizer, AutoModelForCausalLM

# from transformers import TextStreamer
model_name = "/home/liuchi/linkdom/TinyZero/demo/Open-RS1"
model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype="auto", device_map="auto")

tokenizer = AutoTokenizer.from_pretrained("deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B")
# Save the tokenizer to the model directory
tokenizer.save_pretrained(model_name)
print(f"Tokenizer saved to {model_name}")
```
