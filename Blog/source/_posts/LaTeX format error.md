---
title: macOS下VsCode中LaTeX格式化错误
tags:
  - LaTeX
  - VsCode
categories:
  - LaTeX
abbrlink: 23a5f1c5
date: 2022-04-20 14:42:40
---

## 问题描述

在 VS Code 中安装 LaTeX Workshop 插件后，如果按`command + s`保存，会出现如下报错。

> Formatting failed. Please refer to LaTeX Workshop Output for details.

![错误提示](https://pic4.zhimg.com/80/v2-31c0ff38c46cc7384773acc0c46f6ca5.png)

## 解决方法

1. **关闭 LaTeX Workshop 的代码格式化功能**

在`setting.json`中将`“editor.formatOnSave:”`改为`false`

2. **自动格式化LaTeX代码（推荐）**

在终端依次执行以下命令

```bash
sudo /usr/bin/cpan5.18 Unicode::GCString
sudo /usr/bin/cpan5.18 App::cpanminus
sudo /usr/bin/cpan5.18 YAML::Tiny
sudo /usr/bin/perl5.18 -MCPAN -e 'install "File::HomeDir"'
sudo cpan Log::Log4perl
sudo cpan Log::Dispatch
```

在终端中使用`which latexindent`查看所在位置，打开`setting.json`添加配置，指明`latexindent`的安装路径。

```bash
"latex-workshop.latexindent.path": "latexindent所在路径",
```

## 参照资料

- https://github.com/James-Yu/LaTeX-Workshop/issues/376
- https://github.com/Glavin001/atom-beautify/issues/1792