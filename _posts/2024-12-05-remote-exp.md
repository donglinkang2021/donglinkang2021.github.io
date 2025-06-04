---
title: '服务器使用经验贴'
date: 2024-12-05
permalink: /posts/2024/12/remote-exp/
tags:
  - usage
---

## 在服务器中生成自己的ssh key并克隆自己的私有github仓库

```bash
ssh-keygen -t rsa -b 4096 -C "donglinkang2021@163.com" -f ~/.ssh/id_rsa_dlk
```

这行代码用于生成一对 SSH 密钥。具体参数的含义如下：

- `ssh-keygen`: 这是生成 SSH 密钥对的命令。
- `-t rsa`: 指定密钥的类型为 RSA（即使用 RSA 算法）。
- `-b 4096`: 指定密钥的位数为 4096 位，位数越高，密钥越安全。
- `-C "donglinkang2021@163.com"`: 为密钥添加一个注释，通常是电子邮件地址，便于识别。
- `-f ~/.ssh/id_rsa_dlk`: 指定生成的密钥文件的保存路径和文件名。在这里，私钥将保存为 `~/.ssh/id_rsa_dlk`，公钥将保存为 `~/.ssh/id_rsa_dlk.pub`。

执行这条命令后，你将在指定的目录下生成一对新的 SSH 密钥。

```bash
cat /home/liuchi/.ssh/id_rsa_dlk.pub
```

确保ssh-agent在运行，然后将私钥添加到ssh-agent中。

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa_dlk # 可能每次使用git都要执行一下这一步
```

将打印的公钥内容复制到github的ssh key中即可。

自此就能克隆自己的仓库了。

添加github config

```bash
git config --global user.name "Linkdom"
git config --global user.email "donglinkang2021@163.com"
```

## 如何上传自己的仓库到github

由于服务器是多人在用，所以用之前还是得确定一下提交对象：

```bash
git config --global user.name "linkdom"
git config --global user.email "donglinkang2021@163.com"
# 添加ssh密钥
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa_dlk
# 添加远程仓库
git remote add origin git@github.com:donglinkang2021/quantizetransformer.git

# 如果已经新建origin分支了 可以set-url
git remote set-url origin git@github.com:donglinkang2021/quantizetransformer.git

# 上传
git add .
git commit -m "first commit"
git push -u origin master
```

## 使用tmux在后台运行程序

> 比nohup更好用的后台运行程序的工具

```shell
# 创建一个新的session
tmux new -s your_session_name

<run your command here>
<Ctrl+B&D exit>
<Ctrl+B&[ scroll your session>

# 重新连接到session
tmux attach -t your_session_name

# 列出所有session
tmux ls

# 删除session
tmux kill-session -t your_session_name
```

## 查看目录空间大小

1. 查看当前磁盘剩余空间大小，对于报错`No space left on device`有很大帮助。

```bash
df -h .
```

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb1       7.3T  3.8T  3.2T  55% /data1
```

2. 查看当前目录下的文件大小，这样可以找到占用空间大的文件夹并清理文件。

```bash
du -h --max-depth=1
```

```bash
1.3G    ./.cache
7.2M    ./re-anno-transformer
41M     ./makemore
7.0M    ./attention_visualize
265M    ./annotatedtransformer
940M    ./VisionAttention
5.3G    ./.hf_venv
220K    ./diffusion-from-scratch
2.1M    ./SimpleMLP
27M     ./minbpe
23M     ./CT2DcirclePos
96M     ./SimpleTokenizer
2.1G    ./quantizetransformer
3.2G    ./ncccuAI2024
14G     .
```

## huggingface

自己比较喜欢在当前目录新建一个`.bashrc`，在其中配置

```shell
export HF_HOME=/data1/linkdom/.cache/huggingface
# export HF_HOME=/root/autodl-tmp/.cache/huggingface # autodl
export HF_ENDPOINT=https://hf-mirror.com
# source .bashrc
# echo $HF_HOME
# du -h --max-depth=1 $HF_HOME/hub
```

```shell
unset HF_ENDPOINT
```

## pip 换源

在服务器上使用pip安装包时，默认源速度较慢，可以通过以下命令更换为清华大学的源：

```bash
pip install <package_name> -i <mirror_url> --trusted-host <mirror_host>
# example tsinghua
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple/ --trusted-host pypi.tuna.tsinghua.edu.cn
# example ali
pip install -r requirements.txt -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
```

```plaintext
默认源：https://pypi.org/simple
清华大学：https://pypi.tuna.tsinghua.edu.cn/simple/
阿里云：http://mirrors.aliyun.com/pypi/simple/
中国科学技术大学 ：http://pypi.mirrors.ustc.edu.cn/simple/
华中科技大学：http://pypi.hustunique.com/
豆瓣源：http://pypi.douban.com/simple/
腾讯源：http://mirrors.cloud.tencent.com/pypi/simple
华为镜像源：https://repo.huaweicloud.com/repository/pypi/simple/
```

## 参考

- [配置服务器环境 miniconda3+pip换源](https://github.com/donglinkang2021/LKGCN/blob/main/env_set.md)
- [使用huggingface和ollama的一些经验](https://github.com/donglinkang2021/llama3.1-finetune-quick-tuto/blob/main/note.md)
