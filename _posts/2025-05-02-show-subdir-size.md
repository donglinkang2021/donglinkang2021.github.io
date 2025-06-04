---
title: '查看文件夹目录大小'
date: 2025-05-02
permalink: /posts/2025/05/show-subdir-size/
tags:
  - usage
---

**TL;DR:** Use `du -h --max-depth=1 . | sort -hr | head -n 10` to quickly view the top 10 largest directories in the current directory.

<!--more-->

要对 `du -h --max-depth=1 .` 的输出按大小排序，可以使用以下命令组合：

```bash
du -h --max-depth=1 . | sort -h
```

说明：

1. `du -h --max-depth=1 .` - 显示当前目录下各子目录的大小（人类可读格式）
2. `sort -h` - 按人类可读的数字大小进行排序（能正确识别 K、M、G 等单位）

如果想按从大到小排序：

```bash
du -h --max-depth=1 . | sort -hr
```

（`-r` 表示反向排序）

如果只想看最大的几个目录：

```bash
du -h --max-depth=1 . | sort -hr | head -n 10
```

（显示前10个最大的目录）
