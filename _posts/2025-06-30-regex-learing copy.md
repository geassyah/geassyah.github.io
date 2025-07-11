---
layout: post
title:  "regex 正则表达式学习笔记"
date:   2025-06-30 10:00:00 +0800
categories: [Linux, 工具]
tags: [regex, 正则表达式, 学习]
description: "常用正则表达式语法与示例"
comments: true
published: true
media_subpath: /assets/img/
---

## 常用正则表达式语法

| 语法    | 说明                    | 示例           | 匹配内容                |
| ------- | ----------------------- | -------------- | ----------------------- |
| `.`     | 匹配任意单个字符        | `a.b`          | aab, acb, a1b           |
| `*`     | 匹配前面的字符0次或多次 | `ab*`          | a, ab, abb, abbb        |
| `+`     | 匹配前面的字符1次或多次 | `ab+`          | ab, abb, abbb           |
| `?`     | 匹配前面的字符0次或1次  | `ab?`          | a, ab                   |
| `^`     | 匹配行的开头            | `^abc`         | abc在行首               |
| `$`     | 匹配行的结尾            | `abc$`         | abc在行尾               |
| `[]`    | 匹配括号内任一字符      | `[abc]`        | a, b, c                 |
| `[^]`   | 匹配不在括号内的字符    | `[^abc]`       | 除a、b、c以外的任意字符 |
| `{n,m}` | 匹配前面字符n到m次      | `a{2,4}`       | aa, aaa, aaaa           |
| `       | `                       | 或，匹配左或右 | `abc                    | def` | abc或def |
| `()`    | 分组                    | `(abc)+`       | abc, abcabc             |
| `\d`    | 匹配数字                | `\d+`          | 123, 42                 |
| `\w`    | 匹配字母数字下划线      | `\w+`          | abc, 123, a_1           |
| `\s`    | 匹配空白字符            | `\s+`          | 空格、Tab等             |

---

## 常见正则表达式示例

### 1. 章节名
第\d+章 
第1章 xxxxx
第2章 yyyyy
第3章 zzzzz
... .....
第n章 nnnnn

