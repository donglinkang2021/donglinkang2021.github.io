---
title: 'Cursor+Ollama本地模型使用'
date: 2025-01-07
permalink: /posts/2025/01/cursor-ollama-usage/
tags:
  - usage
---

> 由于有时候我们处于局域网的环境，不希望任何数据需要挂梯子出去，所以就可以采取全局域网的形式来写代码，也可以补全，这是比较心仪的状态。

- [windows安装choco](#windows安装choco)
  - [安装Chocolatey](#安装chocolatey)
  - [使用Chocolatey](#使用chocolatey)
  - [更新和卸载软件包](#更新和卸载软件包)
  - [其他常用命令](#其他常用命令)
- [安装配置ngrok](#安装配置ngrok)
- [确保ollama可以成功执行](#确保ollama可以成功执行)
- [cursor中配置](#cursor中配置)
- [More: 使用qwen api](#more-使用qwen-api)
- [Reference](#reference)

## windows安装choco

> 建议在使用choco安装包时都先打开管理员属性比较好

在Windows上使用Chocolatey（通常简称为“Choco”）是一个非常方便的方法来安装、更新和管理软件包。以下是如何在你的Windows系统上安装和配置Chocolatey的步骤：

### 安装Chocolatey

1. **打开PowerShell作为管理员**：
   - 按 `Win + X` 键，然后选择 `Windows PowerShell (管理员)` 或 `Windows Terminal (管理员)`。

2. **运行安装脚本**：
   在打开的PowerShell窗口中，粘贴以下命令并按回车键：

   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```

3. **验证安装**：
   安装完成后，你可以通过运行以下命令来验证Chocolatey是否安装成功：

   ```powershell
   choco --version
   ```

### 使用Chocolatey

一旦Chocolatey安装完成，你就可以使用它来安装各种软件包。例如，要安装Notepad++，你可以运行以下命令：

```powershell
choco install notepadplusplus
```

### 更新和卸载软件包

- **更新所有已安装的软件包**：

  ```powershell
  choco upgrade all
  ```

- **卸载一个软件包**：

  ```powershell
  choco uninstall <package_name>
  ```

### 其他常用命令

- **搜索软件包**：

  ```powershell
  choco search <package_name>
  ```

- **查看已安装的软件包**：

  ```powershell
  choco list
  ```

## 安装配置ngrok

```PowerShell
# 记得先打开管理员的模式
choco install ngrok
# 去https://dashboard.ngrok.com/get-started/setup/windows可以直接看到自己的token其实
ngrok config add-authtoken <token>
# 进行端口映射，cursor是无法识别localhost的，所以需要映射到外网
ngrok http 11434 --host-header="localhost:11434"
```

然后将forwarding的host复制下来，我的是

```plaintext
https://6089-2408-8206-5430-78f8-0-193-b08-c762.ngrok-free.app
```

## 确保ollama可以成功执行

不管是配置在本地的还是配置在服务器上的ollama开放端口后都建议先跑一下简单的程序看看是否可以成功执行，比如：

```python
from openai import OpenAI

client = OpenAI(
    # base_url='http://localhost:11434/v1/', # replace with your forwarding host
    base_url='https://6089-2408-8206-5430-78f8-0-193-b08-c762.ngrok-free.app/v1/',
    api_key='ollama', # required but ignored
)

def test_list_models():
    list_completion = client.models.list()
    for model in list_completion.data:
        print(model)

if __name__ == '__main__':
    test_list_models()
```

比如我的结果如下：

```PowerShell
Model(id='qwen2.5-coder:7b', created=1736214350, object='model', owned_by='library')
Model(id='qwen2.5-coder:3b', created=1736213646, object='model', owned_by='library')
```

## cursor中配置

1. 点开cursor右上角的小齿轮⚙️打开设置，选中Models tab
2. 滑倒openai api key，注意这里好像需要先输入一个正确的api key才能成功，verify之后就可以填其它的了
3. api key此时填写`ollama`，base url填写你的forwarding host，比如我的是`https://6089-2408-8206-5430-78f8-0-193-b08-c762.ngrok-free.app/v1/`
4. 点击save，记得还要在上面model names中点击 add model，然后填写`qwen2.5-coder:7b`

> [!NOTE] 特别注意
> 填入新的api key和base url都要记得先点save和verify一下，如果verify不通过是不会生效的，同时可以把上面的一些model names记得给取消勾选，因为使用了qwen的api key是默认其它都用不了了，==verify的原理是model names中的勾选的model一一去请求响应==

## More: 使用qwen api

将base url改成，token去官网申请即可, 比如[qwen2.5-coder-32b-instruct](https://bailian.console.aliyun.com/#/model-market/detail/qwen2.5-coder-32b-instruct)

```plaintext
https://dashscope.aliyuncs.com/compatible-mode/v1
```

可以使用下面程序先check一下所有可以使用的模型

```python
import os
from openai import OpenAI
from dotenv import load_dotenv

load_dotenv()

client = OpenAI(
    api_key = os.getenv("QWEN_API_KEY"),
    base_url = "https://dashscope.aliyuncs.com/compatible-mode/v1"
)

def test_list_models():
    list_completion = client.models.list()
    for model in list_completion.data:
        print(model)

if __name__ == '__main__':
    test_list_models()
```

## Reference

- [How to use Ollama local LLM in Cursor](https://mem.ai/p/bf6ew6HSm1TDuw7iiR4g)
- [Chocolatey Software | Installing Chocolatey](https://chocolatey.org/install)
- [Cursor + qwen2.5-coder 32b 的配置方式-阿里云开发者社区](https://developer.aliyun.com/article/1640134)
