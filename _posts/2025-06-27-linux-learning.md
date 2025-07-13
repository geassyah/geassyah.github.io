---
layout: post
title:  " Linux 学习笔记 "
date:   2025-06-27 22:20:00 +0800
categories: [Linux]
tags: [Linux, 学习]
description: " 记录常用 Linux 命令以及示例 "
comments: true
published: true
media_subpath: /assets/img/
---

## 常用 Linux 命令

### **sed命令( stream editor)**
  
```bash
sed -i '/s/\r$//g' file # 将 Windows 换行符 `\r\n` 转为 Unix `\n`
sed -i '/s/\r$/\$/g' file # 将行尾的 '\r' 替换为 '$' 符号
```

`sed -i 's/\r$//' file` 这个命令的完整意思是：

-   `sed`: 调用 sed 工具。
-   `-i`: 直接修改文件内容（in-place edit）。
-   `'s/\r$//'`: 对文件的每一行执行这个替换操作：
    -   查找 (`\r$`) 每一行末尾的那个回车符 `\r`。
    -   把它替换为空 (`//` 中间为空)，也就是删除它。
-   `file`: 要操作的文件名。

这个命令的**最终目的**是：**将 Windows/DOS 格式的文本文件（换行符为 `\r\n`）转换为 Unix/Linux 格式的文本文件（换行符为 `\n`）。** 它通过删除多余的 `\r` 来实现这一转换。  

| 命令          | `sed` 模式空间中的操作 | 最终结果   | 目的                                              |
| :------------ | :--------------------- | :--------- | :------------------------------------------------ |
| `'s/\r$//'`   | `line1\r`  -> `line1`  | `line1\n`  | **正确**：将 Windows 换行符 `\r\n` 转为 Unix `\n` |
| `'s/\r$/\$/'` | `line1\r`  -> `line1$` | `line1$\n` | **错误**：将 `\r` 替换成了 `$` 字符               |



### **grep命令(global regular expression)**

```bash
grep -B 5 error file # 在 file 中查找包含 error 字符串的行，显示该行和之前的 5 行
grep -A 5 error file # 在 file 中查找包含 error 字符串的行，显示该行和之后的 5 行
grep -C 5 error file # 在 file 中查找包含 error 字符串的行，显示该行、之前的 5 行和之后的 5 行

grep -n # 显示查找到行的行号
```


### **find命令**
  
```bash
sudo systemctl stop docker  #停用 docker 
```

### **pwd命令**

## 参考资料

- [grep - 菜鸟教程](https://www.runoob.com/linux/linux-comm-grep.html)




