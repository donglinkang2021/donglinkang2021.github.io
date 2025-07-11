---
title: 'scp转移数据'
date: 2025-05-02
permalink: /posts/2025/05/scp-transfer-data/
tags:
  - usage
---

**TL;DR:** This post explains how to use `scp` to transfer data between servers, detailing its behavior when copying directories and handling existing files.

<!--more-->

scp转移数据

```bash
# on 77
cd /data1/linkdom
scp -r .cache liuchi@10.1.114.75:/data2/linkdom
```

使用 `scp` 复制本地目录到目标服务器时，如果目标目录已存在，其行为如下：

1. 覆盖同名文件：  
   目标目录中与源目录同名的文件会被覆盖（无论内容是否更改）。例如，若本地的 `zero/a.txt` 有修改，远程的 `zero/a.txt` 会被替换为本地的版本。

2. 新增文件：  
   源目录中新增的文件会被添加到目标目录中。例如，本地新增的 `zero/c.txt` 会被复制到远程目录。

3. 保留目标独有文件：  
   目标目录中存在但源目录中没有的文件不会被删除。例如，远程的 `zero/d.txt` 和 `zero/e.txt` 会保留，不会被移除。

4. 不处理源删除的文件：  
   如果源目录删除了某些文件（如原 `zero/d.txt`），这些文件在目标目录中仍然存在，不会被自动删除。

#### 示例场景

- 本地 `zero` 目录内容：  
  `a.txt`（已修改）、`b.txt`、`c.txt`（新增）
  
- 远程 `zero` 目录原内容：  
  `a.txt`（旧版）、`b.txt`、`d.txt`、`e.txt`

- 执行 `scp -r zero user@remote:/path` 后：  
  远程目录内容变为：  
  `a.txt`（新版）、`b.txt`、`c.txt`（新增）、`d.txt`（保留）、`e.txt`（保留）

#### 注意事项

- 覆盖机制：  
  `scp` 直接覆盖同名文件，不会检查内容差异，仅根据路径和文件名判断。
  
- 元数据更新：  
  文件的时间戳等元数据可能被更新，取决于 `scp` 的实现。

- 同步建议：  
  如果需要完全同步（包括删除目标多余文件），应使用 `rsync` 命令，例如：  

  ```bash
  rsync -avh --delete /local/zero/ user@remote:/path/zero/
  ```

  （`--delete` 选项会删除目标中存在但源中没有的文件）

> [!tldr] 总结
>
> `scp` 的目录复制是“合并”而非“替换”。它覆盖同名文件并添加新文件，但不会删除目标中独有的文件。若需精确同步，建议使用 `rsync`。
>
> 但是其实`scp`也存在缺点，比如在复制软链接的时候会直接用`scp`的方式复制过去，而不是软链接过去，这样会导致目标服务器的文件占用空间过大。
>
> 自己在实际中迁移 huggingface `.cache`发现就是比原来的文件夹大了一倍，其原因就是因为 `.cache/huggingface/hub/`下的每个模型，本来是`blobs`存的模型的权重，`snapshots`存的是每个模型权重的一系列软链接，和发布在huggingface上保持一致的软链接，在迁移到目标服务器后发现全部被实际的权重文件替代了。
