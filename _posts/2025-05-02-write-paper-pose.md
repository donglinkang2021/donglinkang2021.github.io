---
title: '写论文的一个姿势'
date: 2025-05-02
permalink: /posts/2025/05/write-paper-pose/
tags:
  - notes
---

- 先有后好
- 写论文的正确姿势

之前的写论文的方法比较慢

```python
for chapter in chapters:
  for section in chapter.sections:
    for subsection in section.subsections:
      if content is None:
        content = addcontent()
      while needModify(content):
        modify()
```

决定了，与其每章节多次迭代大大增加写作时间，不如先这样运行自己写论文的思路：

```python
for version in total_versions:
  print(f"version {version} paper")
  for chapter in chapters:
    for section in chapter.sections:
      for subsection in section.subsections:
        if content is None:
          content = addcontent()
        if needModify(content):
          modify()
```

这样做的好处是：

- 增量式改进论文，每次只做到60%就好
- 不断去逼近最终目标，相当于从MC的方法到了one-step TD的方法
- 自己/老师不需要长时间的等待就可以阅读完整论文，从而实现有监督地去及时改进，不会等到做错了太多才改进，在做中改进
- 好文章是改出来的
