---
title: 使用 Jekyll + GitHub Pages 搭建个人博客
excerpt_separator: <!--more-->
description: 
categories:
 - 搭建个人博客
tags: Jekyll, GitHub Pages
---

本次搭建所使用的系统是 `MacOS` 

## 安装 Jekyll

[Jekyll 中文文档](http://jekyllcn.com/docs/home/)

```bash
$ sudo gem install jekyll
```

打开终端，执行上面的指令

## 安装 Next 主题

[Next 官方文档](http://theme-next.simpleyyt.com/)

确保已安装`Ruby 2.1.0` 或更高版本：

```bash
$ ruby --version
```

安装`Bundler`：

```bash
$ gem install bundler
```

下载 NexT 主题：

```bash
$ git clone https://github.com/Simpleyyt/jekyll-theme-next.git
$ cd jekyll-theme-next
```

安装依赖：

```bash
$ bundle install
```

运行 Jekyll：

```bash
$ bundle exec jekyll server
```

此时即可使用浏览器访问 `http://localhost:4000`，检查站点是否正确运行。

<!--more-->

## 创建 GitHub Pages

打开 [GitHub](https://github.com/) ，创建新的 [Repository](https://github.com/new)

命名您的存储库为 `username.github.io`，替换 `username` 为您的 GitHub 用户名。确保它是公共的，并继续告诉GitHub`README`在生成存储库时创建一个文件。

上传（可以使用 `git` 命令或者使用 `git 图形化工具`，也可直接在 github 网页进行上传）刚刚创建的 Next 主题模版文件到 `username.github.io` 库下，等待几分钟，在浏览器输入 `username.github.io` 即可在线浏览您的个人博客了
