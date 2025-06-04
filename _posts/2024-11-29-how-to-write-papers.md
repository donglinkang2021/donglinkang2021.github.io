---
title: 'How to Write Papers'
date: 2024-11-29
permalink: /posts/2024/11/how-to-write-papers/
tags:
  - notes
---

本文是Freeman在CVPR2020上的[分享](http://6.8300.csail.mit.edu/sp22/lectures/L23/ch414243.pdf)

> Only good papers count.

只有好的论文才算数。

## Organization

1. keep audience in mind 有时候需要告诉他们为什么需要关心这个问题
2. 简短说明其它solution为什么不够令人满意，如果足够满意就不需要做这个工作
3. 介绍自己的方法并和其它solutions比较，自己的为什么更好
4. 最后讲讲related work用了similar techniques and experiments，但应用在不同问题上

```plaintext
    1. Introduction
    2. Related work
    3. Main idea
    4. Algorithm
    5. Experiments
    6. Discussion
```

### Introduction

- 必须写得很精彩dynamite

- make it easy for anyone

  - 文章关于什么
  - 解决了什么问题
  - 什么问题比较有趣
  - 什么是new 什么不是
  - 为什么so neat整洁

### Main idea

simple example or toy model to captures the main idea

### Experimental Results

- 读者希望有一个表量化你的算法和之前sota方法的不同结果的表现；

  - 行是不同算法
  - 列是不同的数据或者任务
  - numbers indicate performance

- 如果是一个new task的话，依然要用相关task的算法来解一下看合不合适，并note一下

### How to End the Paper

会议论文往往都有ddl，而不可避免有没做完的一系列结果作者想包含进去：

- 应该抵制在Future work中写“没做完的项目”的诱惑
- 没有比这个更差的方式来结束自己的论文（指出不足之处的总结）

应该这样做：

- 强调论文的贡献
- 读者读完论文世界发生了什么变化
- 开启了哪些新研究方向
- 现在可以做到什么以前做不到的东西

## General Writing Tips

- Keep the reader uppermost in mind
- what does the reader know so far
- what does the reader expect next and why

### Use Fewer Words

- 有力的写作应该是简洁的
- tighter text 是作者可以讲更多的故事，添加更多的figure
- 第二简洁更容易明白

### Title, Figure Captions, and Equations

- 一般来说，标题是最重要的

> [!NOTE] Conjectured relative readership of a CS paper
> Read title >> Look over figures = Read abstract > Read every word

- 因为假设了读者是不会读论文的，所以论文图的caption需要是独立的self-contained
- 这里有一个有意思的观点就是假定读者都是不会读Equations的，全部Equayions替换成blah也要确保行文是流畅的

### Tone

- 可能有时候会对over-sell, hide drawbacks, disparage other’s work感到很大压力，但从短期或者长期interest上来讲都不要屈服于压力
- 自己指出自己工作的shortcoming好过之后等别人来指出
- 我们都是优秀的研究者语调好过唯我独尊的语调，促进小同行之间的友谊
- 建立起clear and reliable reputation，传达对算法表现的精确印象，比如在认为必定work的什么地方却不work了，描述counter-intuitive的事情对于之后也有用

### Author List

论文质量比自己在论文中的位置更加重要

### Avoiding Rejection

拒稿的常见理由

1. authors not deliver what they promise作者没有兑现承诺
2. 缺少重要参考文献，因此怀疑作者不了解问题的sota
3. results too incremental 和之前工作太相似了
4. 结果是否可信，充分tests来支持claims

chair视角中的两类边缘论文

1. cockroach 蟑螂型 没有明显错误，但not exciting

    1. 大概有三分之二会被接收成poster，另外三分之一rejected
    2. 从作者角度，应该work hard to fresh results

2. six-toes puppy delightful paper，但有明显错误

    1. 三分之二reject，三分之一接收poster
    2. 从作者角度，最好等下一轮提交，改掉the easy to spot flaw，下一轮很可能成oral
