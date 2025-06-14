---
title: 'Academic Pages Usage'
date: 2025-06-04
permalink: /posts/2025/06/academic-pages-usage/
tags:
  - usage
---

**TL;DR:** This post records my experience using [AcademicPages](https://github.com/academicpages/academicpages.github.io), including Docker local preview environment configuration, some notes, and some understanding of the Markdown file head.

<!--more-->

记录自己使用 [AcademicPages](https://github.com/academicpages/academicpages.github.io) 的一些经验。

## Docker 本地预览环境配置

> 为了方便在发布之前在本地预览环境，自己选择的是使用 `docker compose up` 来运行 AcademicPages 的 Jekyll 环境。

由于自己是国内的docker，所以在配置 gem 和 bundler 的时候就需要换源，直接从 Dockerfile 开始修改：

```diff
 # Install bundler and dependencies
+RUN gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
+RUN gem sources -l
 RUN gem install connection_pool:2.5.0
 RUN gem install bundler:2.3.26
+RUN bundle config mirror.https://rubygems.org https://gems.ruby-china.com
+RUN bundle config --delete 'mirror.https://rubygems.org'
 RUN bundle install
```

接着改 `Gemfile`，

```diff
-source 'https://rubygems.org'
+source 'https://mirrors.tuna.tsinghua.edu.cn/rubygems/'

 group :jekyll_plugins do
   gem 'jekyll
```

之后自己改了一下基本的配置文件 `_config.yml`：

```bash
# 查看 `_config.yml` 的修改
git diff HEAD^ HEAD _config.yml
```

```diff
 # Basic Site Settings
 locale                   : "en-US"
 site_theme               : "default"
-title                    : "Your Name / Site Title"
+title                    : "Linkdom / Homepage"
 title_separator          : "-"
-name                     : &name "Your Name"
+name                     : &name "Linkdom"
 description              : &description "personal description"
-url                      : https://academicpages.github.io # the base hostname & protocol for your site e.g. "https://[your GitHub username].github.io" or if you already have some other page hosted on Github then use "https://[your GitHub username].github.io/[Your Repo Name]"   
+url                      : https://donglinkang2021.github.io # the base hostname & protocol for your site e.g. "https://[your GitHub username].github.io" or if you already have some other page hosted on Github then use "https://[your GitHub username].github.io/[Your Repo Name]" 
 baseurl                  : "" # the subpath of your site, e.g. "/blog"
-repository               : "academicpages/academicpages.github.io"
+repository               : "donglinkang2021/donglinkang2021.github.io"
```

开始构建：

```bash
# 可以先pull ruby image, 这里自己用了镜像
docker pull docker.1ms.run/ruby:3.2
# 所以也要在Dockerfile中的FROM改为这个镜像
docker compose down
# 第一次可以执行这个命令来清除之前的缓存
docker compose build --no-cache
# 之后如果有些包没下载的话可以不重复下载，接着之前来做
docker compose build
# 启动 AcademicPages 的 Jekyll 环境
docker compose up
```

访问 [http://localhost:4000](http://localhost:4000) 就可以看到 AcademicPages 的预览了。

## 一些注意事项

1. 在使用本地预览的时候，可以先把 `_config.yml` 中的 `future: false` 改为 `true`，这样就可以预览未来的文章了（这其实也是一个文章定时发布的功能）。
2. 把 `_config.yml` 中的 `url:` 改为 `http://localhost:4000` 可以在本地预览的时候正确跳转，否则是直接跳转到 `https://donglinkang2021.github.io` 的对应目录下，看不到修改；**当然改了之后要记得改回来**。
3. 执行`docker compose up`会提示说 `Server address: http://0.0.0.0:4000/`，但实际上我们访问的是 `http://localhost:4000`，所以不需要关心这个提示。
4. 对于文档中的图片，建议使用相对路径，统一放在 `/images/assets/` 目录下（而不要只是放在markdown的相同的一个根目录就好，如果是`./assets`这种会索引不到），这样在本地预览和发布到 GitHub Pages 上都能正确显示。
5. 在写 blog 的时候，建议写完之后在最开头添加一个 `<!--more-->`，这样可以在首页上只显示文章的摘要（自己用`gemini-flash`生成的），而不是全文。自己改了初始`_config.yml`中的 `excerpt_separator: <!--more-->`，这样就可以解决显示太长的问题了。

## 对于 AcademicPages 中 Markdown 文件Head 的一些理解

```bash
---
title: [这里写标题]
date: [按照标准格式日期写，比如2025-06-04]
permalink: [这一个决定了文章的链接地址，/posts/2025/06/academic-pages-usage/，链接地址就是url+permalink]
tags:
  - [这里决定了点击标签可以把同一标签的文章聚合在一起，方便查找，比如notes]
---
```
