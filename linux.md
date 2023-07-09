- [探索 Linux](#探索-linux)
  - [命令行工具](#命令行工具)
  - [文件权限](#文件权限)
  - [shell 函数和脚本不同点](#shell-函数和脚本不同点)

参考

[南京大学 计算机系统基础 课程实验 2021](https://nju-projectn.github.io/ics-pa-gitbook/ics2021/linux.html)
[The Missing Semester of Your CS Education 中文版](https://missing-semester-cn.github.io/)

# 探索 Linux

## 命令行工具

熟悉一些常用的命令行工具, 并强迫自己在日常操作中使用它们

* 文件管理 - `cd`, `pwd`, `mkdir`, `rmdir`, `ls`, `cp`, `rm`, `mv`, `tar`
* 文件检索 - `cat`, `more`, `less`, `head`, `tail`, `file`, `find`
* 输入输出控制 - `重定向：< > >>` `标准错误流：2>` , `|`, `tee`, `xargs`
* 文本处理 - `vim`, `grep`, `awk`, `sed`, `sort`, `wc`, `uniq`, `cut`, `tr`
* 系统监控 - `jobs`, `ps`, `top`, `kill`, `free`, `dmesg`, `lsof`
* 正则表达式

上述工具覆盖了程序员绝大部分的需求

我的使用手册

- [command-line-tools](command-line-tools.md)
- [regular-expression](regular-expression.md)
- [GNUmake](GNUmake.md)


## 文件权限

```
$ ls -l /home
drwxr-xr-x 1 xky  xkyg  4096 Jun 15 missing
```

第 1 字符

- `d` 表示是一个文件夹，`-` 表示是文件

后面 9 个字符 3 个为一组

- 文件拥有者的权限 (xky:rwx)
- 文件所属组的权限 (xkyg:r-x)
- 其他人的权限 (other:r-x)

`r` for 文件夹：are you allowed to see which files are inside this directory.
- eg: `ls someFukingDir  # 如果你没有 r 权限，你的命令无效`

`w` for 文件夹：whether you are allowed to rename, create or remove files within thay directory.
- eg: `someFukingDir/test.c` 如果你有 `test.c` 的 `w`，没有 `someFukingDir` 的 `w`。你能改 `test.c` 的内容，改不了它的名字或者删除它之类的

`x` for 文件夹：are you allowed to enter this directory.
- eg: `cd someFukingDir  # 如果你没有 x 权限，你的命令无效` 


## shell 函数和脚本不同点

- 函数只能与 shell 使用相同的语言，脚本可以使用任意语言
  - 因此在脚本中包含 shebang 是很重要的
- 函数仅在定义时被加载，脚本会在每次被执行时加载
  - 这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次
- 函数会在当前的 shell 环境中执行，脚本会在单独的进程中执行
  - 因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。脚本需要使用 export 将环境变量导出，并将值传递给环境变量
- 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell 脚本中往往也会包含它们自己的函数定义