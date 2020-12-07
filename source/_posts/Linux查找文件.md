---
title: Linux查找文件
tags:
  - Linux
categories:
  - Linux
top: false
date: 2020-06-10 16:20:43
---
# 1.grep
  grep (global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。
  参考：https://man.linuxde.net/grep
## grep常用用法
```bash
[root@www ~]# grep [-acinv] [--color=auto] '搜寻字符串' filename
选项与参数：
-a ：将 binary 文件以 text 文件的方式搜寻数据
-c ：计算找到 '搜寻字符串' 的次数
-i ：忽略大小写的不同，所以大小写视为相同
-n ：顺便输出行号
-v ：反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！
--color=auto ：可以将找到的关键词部分加上颜色的显示喔！

grep命令常见用法
在文件中搜索一个单词，命令会返回一个包含“match_pattern”的文本行：

grep match_pattern file_name
grep "match_pattern" file_name
在多个文件中查找：

grep "match_pattern" file_1 file_2 file_3 ...
输出除之外的所有行 -v 选项：

grep -v "match_pattern" file_name
标记匹配颜色 --color=auto 选项：

grep "match_pattern" file_name --color=auto
使用正则表达式 -E 选项：

grep -E "[1-9]+"
或
egrep "[1-9]+"
只输出文件中匹配到的部分 -o 选项：

echo this is a test line. | grep -o -E "[a-z]+\."
line.

echo this is a test line. | egrep -o "[a-z]+\."
line.
统计文件或者文本中包含匹配字符串的行数 -c 选项：

grep -c "text" file_name
输出包含匹配字符串的行数 -n 选项：

grep "text" -n file_name
或
cat file_name | grep "text" -n

#多个文件
grep "text" -n file_1 file_2
打印样式匹配所位于的字符或字节偏移：

echo gun is not unix | grep -b -o "not"
7:not

#一行中字符串的字符便宜是从该行的第一个字符开始计算，起始值为0。选项 -b -o 一般总是配合使用。
搜索多个文件并查找匹配文本在哪些文件中：

grep -l "text" file1 file2 file3...
```
显示目录中的可执行文件
```bash
ll |grep "*"   # 添加-v可显示非可执行文件
```
grep递归搜索文件
在多级目录中对文本进行递归搜索：

grep "text" . -r -n

忽略匹配样式中的字符大小写：

echo "hello world" | grep -i "HELLO"
hello


# 2.find
> 在 Linux 命令中，find用于在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名，其支持按名称查找、按正则表达式查找、按文件大小查找、按文件权限查找等多种查询方式。如果在使用该命令时，不设置任何参数，则find命令将在当前目录下查找子目录与文件，并且将查找到的子目录和文件全部进行显示。
>
> 参考：https://blog.csdn.net/qq_35246620/article/details/79104520

在 Linux 命令中，find用于在指定目录下查找文件。任何位于参数之前的字符串都将被视为欲查找的目录名，其支持按名称查找、按正则表达式查找、按文件大小查找、按文件权限查找等多种查询方式。如果在使用该命令时，不设置任何参数，则find命令将在当前目录下查找子目录与文件，并且将查找到的子目录和文件全部进行显示。

语法：find + 目标目录(路径) + <选项> + 参数

### 常用选项列表

| 选项                 | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| -perm <权限数值>     | 查找符合指定的权限数值的文件或目录                           |
| -type <文件类型>     | 只寻找符合指定的文件类型的文件                               |
| -name <范本样式>     | 指定字符串作为寻找文件或目录的范本样式                       |
| -expty               | 寻找文件大小为 0 Byte 的文件，或目录下没有任何子目录或文件的空目录 |
| -ls                  | 假设find指令的回传值为ture，就将文件或目录名称列出到标准输出 |
| -maxdepth <目录层级> | 设置最大目录层级                                             |
| -mindepth <目录层级> | 设置最小目录层级                                             |
| -exec <执行指令>     | 假设find指令的回传值为true，就执行该指令                     |
| -ok <执行指令>       | 此参数的效果和指定-exec类似，但在执行指令之前会先询问用户，若回答y或Y，则放弃执行命令 |

### 文件类型参数列表

| 文件类型参数 | 含义     |
| ------------ | -------- |
| `f`          | 普通文件 |
| `l`          | 符号连接 |
| `d`          | 目录     |
| `c`          | 字符设备 |
| `b`          | 块设备   |
| `s`          | 套接字   |
| `p`          | Fifo     |

### 