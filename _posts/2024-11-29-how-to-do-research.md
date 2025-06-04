---
title: 'How to do research'
date: 2024-11-29
permalink: /posts/2024/11/how-to-do-research/
tags:
  - notes
---

**TL;DR:** Research isn't about innate brilliance but consistent effort and strategic direction. Focus on enjoying your work to foster dedication, and remember to steer your efforts effectively. Simplify problems to understand them deeply, and communicate progress by detailing what you've ruled out, not just what's failing. Develop a "toy model" to build intuition, and cultivate a unique research brand by excelling in specific areas.

<!--more-->

> 读一读[How to do research](https://people.csail.mit.edu/billf/publications/How_To_Do_Research.pdf) 更多类似的内容可以看[Course notes for MIT EECS 6.819/6.869 Spring 2022](http://6.8300.csail.mit.edu/sp22/lectures/L23/ch414243.pdf)

积累一些好词

- ace their classes 班上的佼佼者
- flail around 乱来

## Slow down to speed up

走得慢比四处乱窜要好一点。

每一次只改一个变量，这样就可以解释实验的输出结果了。

> [!quote] “It may feel like you’re going slowly, but you’ll be making much more progress than if you flail around, trying different things, but not understanding what’s going on.”
>
> 你可能会觉得自己进展缓慢，但与你东摇西晃、尝试不同的事情但不了解发生了什么相比，你会取得更多的进步。

这句话说的非常对，自己现在在尝试非常多的方向，但有进展、得到一些知识的的时候往往是因为自己沉迷于一个项目之中开始全力以赴，虽然开很多新的项目可以让自己收获到不少自以为的边界利益，但其实没搞懂what’s going on.

## Don’t tell “it doesn’t work“

不要说为什么不work，整个chain中是不可避免出错的，更应该去tell的是：

把问题缩小到哪个范围了，在哪个B步骤之前A步骤依然工作好好的，输入X输出Y，但B步骤就失败了，目前已经排除了W和Z的问题。

> [!NOTE] 又获得一个汇报进度的技巧
>
> 进度还包括：
>
> 1. show why this doesn’t work
> 2. simplify the task to get it start work
> 3. reading some have-to-understand thing before make any progress

还可以从下面一些角度出发：

1. 有simpler case可以work的吗
2. 这个不work的情况是属于general issue影响一类问题下的所有问题吗
3. 到底是什么不work
4. 可以造一个work的例子不
5. 至少可以让它fail worse, 好观察系统的特性

## <span style="color: rgb(235, 82, 247)">Hard work</span>&<span style="color: rgb(121, 83, 227)">Work hard</span>&<span style="color: rgb(255, 32, 32)">Steering</span>

Do research不是一个关乎聪明的问题了，而是关乎努力或者不努力的问题。这里Bill Freeman举例说体育领域同样如此，一直都是hard workers pull ahead.

如何使自己足够努力去做好research呢?

1. 花时间做你喜欢的，你就会慢慢变得擅长，因为你也enjoy it
2. 如果不喜欢做这个问题，知道work hard是做好research的必要条件就行了
3. 同时还需要船舵rudder，做正确的事情
4. time management books to be helpful

## Simple toy model captures the main idea

Build up intuition about what matters是一个research的大优势，记得当时Hinton当时就一直夸赞他的学生Ilya的直觉好。

Because you can figure out what will work by thinking it through with your toy model.

## Strong student\&Everyone else idiot

1. 一个老师让差的学生和好的学生去用同一种方法去解决某一个课题，差学生告诉导师方法不work，好学生告诉导师原方法 $\epsilon$ball内的可行解

2. 有时候认为其他所有人都是垃圾有用，这可以帮助自己去做没人做过的问题。但不要太张扬，可以只是说我想试一试这个方向。

3. 但也要记住有一大批smart人在相关问题上做过工作，不要被一大批不熟悉的相关文献困住

## Nurture your research brand

Business school教会要做自己的brand

- does great work, have good idea, give great talks, write wonderful software
- build up a great reputation

培养自己的优势并play to those strengths

- being broad;creative;great implementer;
