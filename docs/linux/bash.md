---
sidebarDepth: 2
title: Bash
---

# Bash 命令笔记

[阮一峰 Bash 脚本教程](https://wangdoc.com/bash/index.html)命令笔记

## 一、简介

查看当前运行的 Shell

```bash
$ echo $SHELL
/bin/bash
```

查看当前 Linux 系统安装的所有 Shell

```bash
$ cat /etc/shells
```

查看 Bash 版本

```bash
$ bash --version
GNU bash, version 4.4.20(1)-release (x86_64-redhat-linux-gnu)
Copyright (C) 2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

This is free software; you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

# 或者
$ echo $BASH_VERSION
4.4.20(1)-release
```

## 二、基本语法

### 1、echo 命令

`echo`命令的作用是在屏幕输出一行文本，可以将该命令的参数原样输出

```bash
$ echo hello world
hello world
```

输出多行文本需要把多行文本放在引号里面

```bash
$ echo "<HTML>
    <HEAD>
          <TITLE>Page Title</TITLE>
    </HEAD>
    <BODY>
          Page body.
    </BODY>
</HTML>"
```

#### `-n` 参数

默认情况下，`echo` 输出的文本末尾会有一个回车符。`-n` 参数可以取消末尾的回车符

```bash
$ echo -n hello world
hello world$
```

```bash
$ echo a;echo b
a
b

$ echo -n a;echo b
ab
```

#### `-e` 参数

`-e` 参数会解释引号（双引号和单引号）里面的特殊字符,如果不使用-e 参数，即默认情况下，引号会让特殊字符变成普通字符，echo 不解释它们，原样输出

```bash {2,5,8,9}
$ echo hello\nWorld
hellonWorld

$ echo "hello\nworld"
hello\nworld

$ echo -e "hello\nworld"
hello
world
```

### 2、命令格式

Shell 命令一般的格式

```bash
$ command [ arg1 ... [ argN ]]
```

Bash 单个命令一般都是一行，在每一行的结尾加上反斜杠可以写成多行

```bash
$ echo foo bar

# 等同于
$ echo foo \
bar
```

### 3、空格

参数之间有多个空格，Bash 会自动忽略多余的空格

```bash
$ echo this is a     test
this is a test
```

### 4、分号

上一个命令执行结束后，无论成功与否，再执行第二个命令

```bash
$ clear; ls
```

### 5、命令的组合符`&&`和`||`

command1 无论成功与否，都会继续执行 command2 命令

```bash
command1; command2
```

command1 执行成功，继续执行 command2 命令

```bash
command1 && command2
```

command1 执行失败，才会继续执行 command2 命令

```bash
command1 || command2
```

### 6、type 命令

`type`命令用来判断命令的来源

```bash
$ type echo
echo is a shell builtin
$ type ls
ls is hashed (/bin/ls)
```

查看一个命令的所有定义

```bash
$ type -a echo
echo is shell builtin
echo is /usr/bin/echo
echo is /bin/echo
```

type 命令的-t 参数，可以返回一个命令的类型：
:::details 命令类型
| 关键字 | 命令类型 |
| -------- | -------- |
| alias | 别名 |
| keyword | 关键词 |
| function | 函数 |
| builtin | 内置命令 |
| file | 文件 |
:::

```bash
$ type -t bash
file
$ type -t if
keyword
```

### 7、常用快捷键

- `Ctrl + U`：从光标位置删除到行首
- `Ctrl + A`：从光标位置移到到行首
- `Ctrl + K`：从光标位置删除到行尾
- `Ctrl + E`：从光标位置移到行尾
- `Ctrl + L`：清除屏幕并将当前行移到页面顶部
- `Ctrl + C`：中止当前正在执行的命令
- `Shift + PageUp`：向上滚动
- `Shift + PageDown`：向下滚动
- `Ctrl + D`：关闭 Shell 会话
- `↑，↓`：浏览已执行命令的历史记录

## 三、模式扩展`globbing`

### 1、八种扩展

- 波浪线扩展
- `?`字符扩展
- `*`字符扩展
- 方括号扩展
- 大括号扩展
- 变量扩展
- 子命令扩展
- 算术扩展

`Bash` 允许用户关闭扩展

```bash
$ set -o noglob
# 或者
$ set -f
```

重新打开扩展

```bash
$ set +o noglob
# 或者
$ set +f
```

### 2、波浪线扩展

```bash
$ echo ~
/home/me
```

`~user`表示扩展成用户`user`的主目录

```bash
$ echo ~foo
/home/foo # 用户foor的主目录

$ echo ~root
/root # root用户的主目录
```

`~+`会扩展成当前所在的目录，等同于`pwd`命令

```bash
$ cd ~/foo
$ echo ~+
/home/me/foo
```

### 3、`?`字符扩展

`?`字符代表文件路径里面的任意单个字符，不包括空字符，如果匹配多个字符，就需要多个`?`连用

```bash
# 存在文件 a.txt 和 b.txt
$ ls ?.txt
a.txt b.txt

# 存在文件 a.txt、b.txt 和 ab.txt
$ ls ??.txt
ab.txt

# 当前目录为空目录
$ echo ?.txt
?.txt
```

### 4、`*`字符扩展

## 四、引号和转义

## 五、变量

## 六、字符串操作

## 七、算术运算

## 八、行操作

## 九、目录堆栈

## 十、脚本入门

## 十一、read 命令

## 十二、条件判断

## 十三、循环

## 十四、函数

## 十五、数组

## 十六、set 命令，shopt 命令

## 十七、脚本除错

## 十八、mktemp 命令，trap 命令

## 十九、启动环境

## 二十、命令提示符
