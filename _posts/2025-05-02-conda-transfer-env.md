---
title: 'conda迁移环境'
date: 2025-05-02
permalink: /posts/2025/05/conda-transfer-env/
tags:
  - usage
---

**TL;DR:** This guide explains how to transfer a conda environment from a server with internet access to one without, using `conda-pack` to create a portable environment package.

<!--more-->

需求：将有网的服务器打包conda环境迁移到无网的环境

```bash
# 10.1.114.77:/home/liuchi/linkdom
conda activate linkdom
pip install conda-pack
conda pack -n zero -o linkdom_zero.tar.gz --ignore-editable-packages # 如果不加这一行会报错，有些包可以过去之后再下
scp linkdom_zero.tar.gz liuchi@10.1.114.75:/home/liuchi/linkdom/
# 10.1.114.75:/home/liuchi/linkdom
mkdir -p /home/liuchi/anaconda3/envs/zero
tar -xzvf linkdom_zero.tar.gz -C /home/liuchi/anaconda3/envs/zero
```

按照上面这样搞是可以确保成功的。

> [!error] 避雷
> 直接scp整个环境过去是不行的（当使用比如`vllm`这种工具时会遇到bug）。
>
> ```bash
> scp -r /home/liuchi/miniconda3/envs/zero liuchi@10.1.114.75:/home/liuchi/anaconda3/envs/
> ```
