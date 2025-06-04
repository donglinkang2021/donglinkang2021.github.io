---
title: 'How to use mkdocs-material'
date: 2022-09-20
permalink: /posts/2022/09/how-to-use-mkdocs-material/
tags:
  - usage
---

**TL;DR:** This article is about how to use mkdocs-material to build a blog. It introduces how to install and configure the mermaid plugin and how to use mathjax to display mathematical formulas.

<!--more-->

> 感觉自己的学习的方向得开始有所改变了，看博客只能慢慢开始当乐呵乐呵，真正想会用得去看原文档，学习文档是相当快的方法，再者我们要学习别人仓库里的代码，到达大佬的程度之后，我们就看可以直接学习论文本身了，设计框架，在一步步细化，自己搭建自己的大厦hiahia...
> 这个博客纯当是自己学习文档的开始吧。

[mkdocs-material](https://squidfunk.github.io/mkdocs-material/setup/)

> 先看看效果，随缘更新

## mermaid 测试

由于要用到mermaid，所以需要安装插件，具体操作如下：

```bash
pip install mkdocs-mermaid2-plugin
```

执行完上述命令后，需要在`mkdocs.yml`中添加如下配置：

```yaml
plugins:
  - search
  - mermaid2
```

还要在`mkdocs.yml`中添加如下配置：

```yaml
extra_javascript:
  - https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js
```

我本来是用的unpkg，但是发现有时候会加载不出来，所以改成了jsdelivr，这个是国内的，速度应该会快一点。

```yaml
extra_javascript:
    - https://unpkg.com/mermaid/dist/mermaid.min.js
```

### reference

[https://github.com/fralau/mkdocs-mermaid2-plugin](https://github.com/fralau/mkdocs-mermaid2-plugin)

```mermaid
graph TD
    A[Christmas] -->|Get money| B(Go shopping)
    B --> C{Let me think}
    C -->|One| D[Laptop]
    C -->|Two| E[iPhone]
    C -->|Three| F[Car]
```

## 数学公式测试

$$
\begin{aligned}
\frac{1}{2} & = \frac{1}{2} \\
\frac{1}{2} & = \frac{1}{2} \\
\frac{1}{2} & = \frac{1}{2} \\
\frac{1}{2} & = \frac{1}{2} \\
\end{aligned}
$$

大型测试

$$
\begin{aligned}
X^{[1]}&=\begin{bmatrix}
 \mid  & \mid &\mid \\
 x^{[1](1)} & x^{[1](2)}  &x^{[1](3)}\\
 \mid & \mid &\mid
\end{bmatrix}
\\
z^{[1](1)}&=W^{[1]}x^{[1](1)}+b^{[1]}\\
\rightarrow
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\
 \cdot \\
\end{bmatrix}}\limits_{(1)}
&=\begin{bmatrix}
 \cdot&\cdot&\cdot \\
 \cdot&\cdot&\cdot \\
 \cdot&\cdot&\cdot\\
 \cdot&\cdot&\cdot\\
\end{bmatrix}
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\
\end{bmatrix}}\limits_{(1)}
+
\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\
 \cdot \\
\end{bmatrix}\\
Z^{[1]}&=W^{[1]}X^{[1]}+b^{[1]}\\
\rightarrow
\begin{bmatrix}
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\
 \cdot \\
\end{bmatrix}}\limits_{(1)}
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\
 \cdot \\
\end{bmatrix}}\limits_{(2)}
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\
 \cdot \\
\end{bmatrix}}\limits_{(3)}
\end{bmatrix}&=
\begin{bmatrix}
 \cdot&\cdot&\cdot \\
 \cdot&\cdot&\cdot \\
 \cdot&\cdot&\cdot\\
 \cdot&\cdot&\cdot\\
\end{bmatrix}
\begin{bmatrix}
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\ 
\end{bmatrix}}\limits_{(1)}
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\ 
\end{bmatrix}}\limits_{(2)}
\mathop{\begin{bmatrix}
 \;\cdot\; \\
 \cdot \\
 \cdot \\ 
\end{bmatrix}}\limits_{(3)}
\end{bmatrix}
+\begin{bmatrix}
 \cdot&\cdot&\cdot \\
 \cdot&\cdot&\cdot \\
 \cdot&\cdot&\cdot\\
 \cdot&\cdot&\cdot\\
\end{bmatrix}
\\&注:由于广播b的每一列应该都是一样的
\end{aligned}
$$

reference：[https://squidfunk.github.io/mkdocs-material/reference/mathjax/?h=math](https://squidfunk.github.io/mkdocs-material/reference/mathjax/?h=math)
