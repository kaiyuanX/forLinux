- [探索 Linux](#探索-linux)
  - [命令行工具](#命令行工具)
  - [文件权限](#文件权限)
  - [shell 函数和脚本不同点](#shell-函数和脚本不同点)
  - [配置文件（Dotfiles）](#配置文件dotfiles)
      - [可移植性](#可移植性)

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

## 配置文件（Dotfiles）

工具通过_点文件_进行配置：

*   `bash` - `~/.bashrc`, `~/.bash_profile`
*   `git` - `~/.gitconfig`
*   `vim` - `~/.vimrc` 和 `~/.vim` 目录
*   `ssh` - `~/.ssh/config`
*   `tmux` - `~/.tmux.conf`

如何管理这些配置文件

它们应该在它们的文件夹下，并使用版本控制系统进行管理，然后通过脚本将其 **符号链接** 到需要的地方

这么做有如下好处：

*   **安装简单**: 如果您登录了一台新的设备，在这台设备上应用您的配置只需要几分钟的时间
*   **可移植性**: 您的工具在任何地方都以相同的配置工作
*   **同步**: 在一处更新配置文件，可以同步到其他所有地方
*   **变更追踪**: 您可能要在整个程序员生涯中持续维护这些配置文件，而对于长期项目而言，版本历史是非常重要的

配置文件中需要放些什么？您可以通过在线文档和[帮助手册](https://en.wikipedia.org/wiki/Man_page)了解所使用工具的设置项。另一个方法是在网上搜索有关特定程序的文章，作者们在文章中会分享他们的配置。还有一种方法就是直接浏览其他人的配置文件：您可以在这里找到无数的[dotfiles 仓库](https://github.com/search?o=desc&q=dotfiles&s=stars&type=Repositories) —— 其中最受欢迎的那些可以在[这里](https://github.com/mathiasbynens/dotfiles)找到（我们建议您不要直接复制别人的配置）。[这里](https://dotfiles.github.io/) 也有一些非常有用的资源。

本课程的老师们也在 GitHub 上开源了他们的配置文件： [Anish](https://github.com/anishathalye/dotfiles), [Jon](https://github.com/jonhoo/configs), [Jose](https://github.com/jjgo/dotfiles)

#### 可移植性

配置文件的一个常见的痛点是它可能并不能在多种设备上生效

- 例如，如果您在不同设备上使用的操作系统或者 shell 是不同的，则配置文件是无法生效的
- 有时您仅希望特定的配置只在某些设备上生效

有一些技巧可以轻松达成这些目的。如果配置文件 if 语句，则您可以借助它针对不同的设备编写不同的配置。例如，您的 shell 可以这样做：

```bash
if [[ "$(uname)" == "Linux" ]]; then {do_something}; fi

# 使用和 shell 相关的配置时先检查当前 shell 类型
if [[ "$SHELL" == "zsh" ]]; then {do_something}; fi

# 您也可以针对特定的设备进行配置
if [[ "$(hostname)" == "myServer" ]]; then {do_something}; fi
```

如果配置文件支持 include 功能，您也可以多加利用。例如：`~/.gitconfig` 可以这样编写：

```
[include]
    path = ~/.gitconfig_local
```

然后我们可以在日常使用的设备上创建配置文件 `~/.gitconfig_local` 来包含与该设备相关的特定配置。您甚至应该创建一个单独的代码仓库来管理这些与设备相关的配置。

如果您希望在不同的程序之间共享某些配置，该方法也适用。例如，如果您想要在 `bash` 和 `zsh` 中同时启用一些别名，您可以把它们写在 `.aliases` 里，然后在这两个 shell 里应用：

```
# Test if ~/.aliases exists and source it
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```
