---
layout: post
title:  "欢迎来到 Jekyll 世界：一篇测试文章"
date:   2023-10-27 10:30:00 +0800
author: "geassyah"
categories: [Jekyll, Testing]
tags: [Markdown, 示例, Front Matter, Liquid]
description: "这是一篇用于测试 Jekyll 博客功能的示例文章，其中包含了各种常见的 Markdown 元素和 Jekyll 特有的 Liquid 标签。"
comments: true
published: true
---

欢迎！这是一篇用来展示和测试 Jekyll 网站功能的文章。如果你能正常看到这篇文章，说明你的 Jekyll 环境已经配置成功。

这篇文章包含了所有常见的 Markdown 元素，可以帮助你测试你的主题样式。

## YAML Front Matter

文章开头的 `---` 包裹的部分就是 YAML Front Matter。它定义了这篇文章的元数据，比如标题、日期、分类和标签。Jekyll 会使用这些数据来组织和渲染页面。

例如，这篇文章的标题是 `{{ page.title }}`，它就是通过 Liquid 标签从 Front Matter 中读取的。

## Markdown 基础元素

下面是一些常见的 Markdown 元素，你可以检查它们在你的主题中是否显示正常。

### 文本格式

这是普通的段落文本。

**这是加粗的文本。**

*这是斜体的文本。*

***这是加粗且斜体的文本。***

~~这是带删除线的文本。~~

你可以使用 `行内代码` 来突出显示代码或命令。

### 标题

# 这是一级标题 (H1)
## 这是二级标题 (H2)
### 这是三级标题 (H3)
#### 这是四级标题 (H4)
##### 这是五级标题 (H5)
###### 这是六级标题 (H6)

### 列表

**无序列表:**
- 列表项 A
- 列表项 B
  - 嵌套列表项 B1
  - 嵌套列表项 B2
- 列表项 C

**有序列表:**
1. 第一个列表项
2. 第二个列表项
3. 第三个列表项
   1. 嵌套的第一个列表项
   2. 嵌套的第二个列表项

### 链接和图片

**链接:**
这是一个指向 [Jekyll 官网](https://jekyllrb.com/) 的链接。

**图片:**
![Jekyll Logo](https://jekyllrb.com/img/logo-2x.png "Jekyll Logo")
*图片下方的说明文字*

### 引用

> “Stay hungry, stay foolish.”
>
> 这是一个经典的引用。你可以用它来引用名言或者突出显示某段文字。

### 代码块

代码块是技术博客中非常重要的部分。Jekyll 通常使用 Rouge 来实现语法高亮。

**Ruby 代码示例:**
```ruby
def hello_jekyll
  puts "Hello, Jekyll!"
end

hello_jekyll()
