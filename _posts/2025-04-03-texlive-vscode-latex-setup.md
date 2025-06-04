---
title: '本地配置Texlive + Latex Workshop(VSCode latexmk)愉快书写'
date: 2025-04-03
permalink: /posts/2025/04/texlive-vscode-latex-setup/
tags:
  - notes
---

**TL;DR:** This guide provides a step-by-step approach to setting up a local TeX Live environment and configuring the LaTeX Workshop extension in VSCode for a smooth LaTeX writing experience. It emphasizes offline installation of TeX Live to avoid common online installation issues and offers a tested configuration for VSCode to compile LaTeX projects efficiently.

<!--more-->

## 安装texlive

> [!NOTE] 避雷在线下载
>
> 自己尝试过去官网 [Installing TeX Live over the Internet - TeX Users Group](https://www.tug.org/texlive/acquire-netinstall.html) 下载了一个 20多MB的`install-tl-windows.exe`去执行下载，但是整整花了差不多中午四个多小时的结果之后卡在了一个命令上 `running package-specific postactions`，参考了[[3]](https://ask.latexstudio.net/ask/article/833.html)的解决方案，最终决定使用离线下载+no gui的下载方式。

推荐离线下载：直接从北理工校园网去 [mirrors.bit.edu.cn/CTAN/systems/texlive/Images/](https://mirrors.bit.edu.cn/CTAN/systems/texlive/Images/) 下载镜像 `texlive2025.iso`(有`6GB`大，建议选择合适的硬盘安装，装完后的`texlive`文件夹有`9.02GB`，最后可以有20G空间)，自己的校园网是10MB/s的速度，下载了大概10分钟就好了。如果是校外可以参考下面的链接，去下载`texlive20xx.iso`镜像文件。

- 清华大学（北京）：https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/texlive2025.iso
- 上海交通大学（上海）：https://mirrors.sjtug.sjtu.edu.cn/ctan/systems/texlive/Images/texlive2025.iso
- 中国科技大学（合肥）：https://mirrors.ustc.edu.cn/CTAN/systems/texlive/Images/texlive2025.iso
- 重庆大学（重庆）：https://mirrors.cqu.edu.cn/CTAN/systems/texlive/Images/texlive2025.iso
- 腾讯云：https://mirrors.cloud.tencent.com/CTAN/systems/texlive/Images/texlive2025.iso
- 阿里云：https://mirrors.aliyun.com/CTAN/systems/texlive/Images/texlive2025.iso

接着双击下载好的镜像，会自动挂载到`D盘`，然后要运行`install-tl-windows.bat`安装程序，我们根据[[3]](https://ask.latexstudio.net/ask/article/833.html)采用no gui的下载方式。

![alt text](/images/assets/latex-tuto/image.png)

管理员打开cmd

![alt text](/images/assets/latex-tuto/image-1.png)

输入下面命令开始安装：

```shell
D:
install-tl-windows.bat --no-gui
```

> [!NOTE] 注意
>
> 执行完上面命令后，建议看着那个cmd，等到最后结束，安装的时间有点久（大概是二十分钟左右），中途电脑休眠了会导致没有继续安装；自己猜测这个bat里面的原理是一步一步将镜像文件中的tar.gz文件解压到指定的目录中，所以需要时间。更多细节可以参考[[3]](https://ask.latexstudio.net/ask/article/833.html)。

## VSCode配置latex Workshop

这个部分主要参考[[2]](https://bithesis.bitnp.net/guide/configure-and-compile.html)的内容，假设你已经装好了VSCode和latex Workshop插件，接下来去配置settings.json文件。

![alt text](/images/assets/latex-tuto/image-2.png)

<kbd>Ctrl</kbd> + <kbd>,</kbd> 打开设置，搜索`settings.json`，点击右上角的`Open Settings (JSON)`(上图红色箭头)，在最下面添加下面的配置：

```json
    "latex-workshop.latex.tools": [
        {
            "name": "latexmk",
            "command": "latexmk",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-xelatex",
                "-outdir=%OUTDIR%",
                "-cd",
                "%DOC%"
            ],
            "env": {}
        },
        {
            "name": "xelatex",
            "command": "xelatex",
            "args": [
            "-synctex=1",
            "-interaction=nonstopmode",
            "-file-line-error",
            "-pdf",
            "-output-directory=%OUTDIR%",
            "-cd",
            "%DOC%"
            ],
            "env": {}
        },
        {
            "name": "bibtex",
            "command": "bibtex",
            "args": [
                "%OUTDIR%/%DOCFILE%"
            ],
            "env": {}
        }
    ],
    "latex-workshop.latex.recipes": [
        {
            "name": "latexmk",
            "tools": [
                "latexmk"
            ]
        },
        {
            "name": "xelatex -> bibtex -> xelatex * 2",
            "tools": [
                "xelatex",
                "bibtex",
                "xelatex",
                "xelatex"
            ]
        }
    ],
    "latex-workshop.latex.outDir": ".output",
    "latex-workshop.view.pdf.internal.synctex.keybinding": "double-click",
```

至此我们配置好了latex的编译链xelatex -> bibtex -> xelatex * 2，更多的详细编译方法可以自行查看[[2]](https://bithesis.bitnp.net/guide/configure-and-compile.html)。

## 快速测试

在VSCode中创建一个新的`.tex`文件，输入下面的代码：

```latex
% !TEX program = xelatex
\documentclass[12pt,a4paper]{article}

% 导入中文支持
\usepackage{ctex}
\usepackage{graphicx}
\usepackage{amsmath}
\usepackage{listings}
\usepackage{color}

\title{LaTeX 测试文档}
\author{测试用户}
\date{\today}

\begin{document}

\maketitle

\section{简介}
这是一个简单的 \LaTeX{} 测试文档，用于测试您的 LaTeX Workshop 配置。

\section{数学公式}
以下是一些数学公式示例：

行内公式：$E = mc^2$

行间公式：
\begin{equation}
    \int_{a}^{b} f(x) \, dx = F(b) - F(a)
\end{equation}

\section{表格}
下面是一个简单的表格：

\begin{center}
\begin{tabular}{|c|c|c|}
    \hline
    列1 & 列2 & 列3 \\
    \hline
    数据1 & 数据2 & 数据3 \\
    \hline
\end{tabular}
\end{center}

\section{代码}
下面是一段代码示例：

\begin{lstlisting}[language=Python]
def hello_world():
    print("Hello, LaTeX!")
\end{lstlisting}

\section{图片}
要插入图片，请使用以下命令（需要图片文件存在）：

\begin{figure}[h]
    \centering
    \includegraphics[width=1\columnwidth]{example.jpg}
    \caption{示例图片}
    \label{fig:example}
\end{figure}

\end{document} 
```

按住<kbd>Ctrl</kbd> + <kbd>S</kbd>自动保存，VSCode会自动编译这个文件；或者点击左侧的`TeX`图标再点击Build或者两个Recipe其中一个都可以。

![alt text](/images/assets/latex-tuto/image-3.png)

至此已经完成了本地的texlive + latex Workshop(VSCode)的配置，接下来就可以愉快的书写毕业论文了。

> [!NOTE] Latex Workshop常用快捷键
> 
> 1. tex -> pdf: <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>J</kbd>
> 2. pdf -> tex: <kbd>Ctrl</kbd>+cursor click or double click (有可能随着vscode版本更新出现暂时用不了的情况)
> 3. build: <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>B</kbd>
> 4. clean useless compiled file: <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>C</kbd>

## Reference

1. [🍌 如何开始 | BIThesis](https://bithesis.bitnp.net/guide/getting-started.html)
2. [📃 编辑器配置与模板编译 | BIThesis](https://bithesis.bitnp.net/guide/configure-and-compile.html)
3. [不再卡在running package-specific postactions，手把手教你无界面安装 TeXLive 2025 - LaTeX问答](https://ask.latexstudio.net/ask/article/833.html)
4. [TeX Live 2021 从卸载到安装指南_texlive卸载-CSDN博客](https://blog.csdn.net/weixin_45839039/article/details/115460955)

> PS: 中间遇到任何无法解决的困难都可以试着卸载重装（参考4），毕竟重装解决99%问题
