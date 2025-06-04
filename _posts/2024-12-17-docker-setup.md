---
title: '配置docker记录'
date: 2024-12-17
permalink: /posts/2024/12/docker-setup/
tags:
  - usage
---

**TL;DR:** This post documents the process of setting up a Docker container with Miniconda, troubleshooting common issues like network errors during image pulling, and configuring Docker to use image accelerators. It also covers creating a new container with GPU support, shared memory, and volume mounting, along with steps to activate the environment and run code within the container.

<!--more-->

## 配置环境

先拉取镜像

```bash
docker pull continuumio/miniconda3:latest
```

报错

```bash
(base) root@ubuntu20:~/ASM# docker pull continuumio/miniconda3:latest
Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
```

### 使用image 加速器

> 参考 [docker镜像源配置](https://blog.csdn.net/buluxianfeng/article/details/143977194)

```bash

# 创建目录
sudo mkdir -p /etc/docker
 
# 写入配置文件
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://docker.unsee.tech",
        "https://dockerpull.org",
        "https://docker.1panel.live",
        "https://dockerhub.icu"
    ]
}
EOF
 
# 重启docker服务
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 报错

```bash
docker exec -it asm_test2 bash # 10g
```

## 重新新建一个容器

```bash
docker run -itd --network=host --gpus all --shm-size=10g -v /root/ASM:/home --name asm_test2 continuumio/miniconda3 /bin/bash
```

文件挂载成功了，现在开始跑一下代码看看

- 添加进度条

```bash
bash generate_imdb_model.sh
```

重新激活环境

```bash
docker start asm_test2
```
