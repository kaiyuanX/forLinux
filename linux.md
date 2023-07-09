- [总纲](#总纲)
- [如何寻求帮助？](#如何寻求帮助)
    - [man](#man)
    - [tldr](#tldr)
    - [info](#info)
    - [help](#help)
- [如何简单操作？](#如何简单操作)
    - [光标](#光标)
    - [Tab 补全](#tab-补全)
- [常用命令 ](#常用命令)
    - [重定向](#重定向)
    - [cd](#cd)
    - [ls](#ls)
    - [pwd](#pwd)
    - [mkdir](#mkdir)
    - [rm](#rm)
    - [cp](#cp)
    - [mv](#mv)
    - [touch](#touch)
    - [cat](#cat)
    - [more](#more)
    - [less](#less)
    - [nano](#nano)
    - [reboot](#reboot)
    - [poweroff](#poweroff)
    - [ping](#ping)
    - [grep](#grep)
    - [mount](#mount)
    - [umount](#umount)
    - [tar](#tar)
    - [ln](#ln)
    - [chown](#chown)
    - [chmod](#chmod)
    - [useradd](#useradd)
    - [passwd](#passwd)
    - [whereis](#whereis)
    - [find](#find)
    - [wget](#wget)
    - [sort](#sort)
    - [uniq](#uniq)
    - [xdg-open](#xdg-open)
- [文件权限](#文件权限)
- [shell 函数和脚本不同点](#shell-函数和脚本不同点)

参考

[新手指南： Linux 新手应该知道的 26 个命令](https://linux.cn/article-6160-1.html)
[南京大学 计算机系统基础 课程实验 2021](https://nju-projectn.github.io/ics-pa-gitbook/ics2021/linux.html)
[The Missing Semester of Your CS Education 中文版](https://missing-semester-cn.github.io/)

## 总纲

熟悉一些常用的命令行工具, 并强迫自己在日常操作中使用它们

* 文件管理 - `cd`, `pwd`, `mkdir`, `rmdir`, `ls`, `cp`, `rm`, `mv`, `tar`
* 文件检索 - `cat`, `more`, `less`, `head`, `tail`, `file`, `find`
* 输入输出控制 - `重定向：< > >>` `标准错误流：2>` , `|`, `tee`, `xargs`
* 文本处理 - `vim`, `grep`, `awk`, `sed`, `sort`, `wc`, `uniq`, `cut`, `tr`
* 系统监控 - `jobs`, `ps`, `top`, `kill`, `free`, `dmesg`, `lsof`
* 正则表达式

上述工具覆盖了程序员绝大部分的需求


## 如何寻求帮助？

#### man

`man` 是 Linux 的帮助手册，即 `manual`

执行以后，在 **man page** 页面中按 `q` 退出

获取 `ls` 的帮助

```
$ man ls
```

查看有多少 `ls` 同名的手册

```
$ man -f ls          # -f 单词匹配；-k 选项匹配范围更宽松
ls (1)               - list directory contents
ls (1p)              - list directory contents
```

查看上诉的某一个手册

```
$ man 1p ls
```

#### tldr

tldr pages: Too Long; Didn't Read

它简化了烦琐的 man pages，仅列出某指令常用的使用方法

#### info

与 `man` 不同的是，可以像浏览网页一样在各个节点中跳转。

从文档首页开始浏览

```
$ info
```

获取特定程序的帮助

```
$ info program
```

#### help

除了上面的两种方法外，还有一种简单使用的方法，那就是 `--help` 参数，一般程序都会有这个参数，会输出最简单有用的介绍

```
$ man --help       # 获取 man 的帮助
```

## 如何简单操作？

在 `Terminal（终端）` 中，有许多操作技巧，这里就介绍几个简单的。 

#### 光标

* Home 移动光标到本行开头
* End 移动光标到本行结尾
* PgUp 向上翻页
* PaDN 向下翻页
* ctrl + c 终止当前程序

#### Tab 补全

`Tab` 补全是非常有用的一个功能，可以用来自动补全命令或文件名，省时准确。

*   未输入状态下连按**两次** `Tab` 列出所有可用命令
*   已输入部分命令名或文件名，按 `Tab` 进行自动补全，多用你就肯定会喜欢的了。

## 常用命令 

以下命令按照通常的使用频度排列

#### 重定向

`1>&2` stdout 重定向到 stderr，`&2` 表示 stderr

`2>&1` stderr 重定向到 stdout，`&1` 表示 stdout

`&>` 是指将 stdout 与 stderr 一起重定向到某个地方

举个例子
```bash
$ ls a.txt b.txt 1> file.out 2>&1
$ cat file.out
a.txt
ls: b.txt: No such file or directory

$ cat file1.txt file2.txt &> out.txt
```

#### cd

`cd` 是打开某个路径的命令，也就是打开某个文件夹，并跳转到该处

```
$ cd path      # path 为你要打开的路径。
```

其中 path 有绝对路径和相对路径之分

绝对路径强调从 `/` 起，一直到所在路径

另外在 Linux 中， `.` 代表当前目录， `..` 代表上级目录，因此返回上级目录 `cd ..`

#### ls

( list )

```
$ ls       
$ ls -l    # 可见文件详细信息
$ ls -hl   # 可见文件详细信息并以可读大小显示文件大小
$ ls -al   # 所有文件（包括隐藏）的详细信息
```

**注意**： Linux 中 以 `.` 开头的文件或文件夹均为隐藏文件或隐藏文件夹

#### pwd

`pwd` 用于返回当前工作目录的绝对路径

print working directory

#### mkdir

`mkdir` 用于新建文件夹

```
$ mkdir folder
$ mkdir -p folder/subfolder    # -p ：当父目录不存在时则建立，用此参数可建立多级文件夹
```

#### rm

`rm` 删除文件 ( remove ) 

```
$ rm filename      # 删除 filename
$ rm -i filename   # 删除 filename 前提示，若多个文件则每次提示
$ rm -rf folder/subfolder/  # 递归删除 subfolder 下所有文件及文件夹，包括 subfolder 自身
$ rm -d folder     #  删除空文件夹
```

#### cp

`cp` 复制文件 ( copy )

```
$ cp source dest            # 将 source 复制到 dest
$ cp folder/*  dest         # 将 folder 下所有文件(不含子文件夹中的文件)复制到 dest
$ cp -r folder  dest        # 将 folder 下所有文件（包含子文件夹中的所有文件）复制到 dest
```

#### mv

`mv` 移动文件 ( move )

```
$ mv source folder         # 将 source 移动到 folder 下，完成后则为  folder/source
$ mv source dest           # 在 dest 不为目录的前提下，重命名 source 为 dest
$ mv -i source folder      # 在移动时，若文件已存在则提示 **是否覆盖** 
```

#### touch

可以创建一个内容为空的新文件NEWFILE, 若NEWFILE已存在, 其内容不会丢失

```
$ touch NEWFILE
```

#### cat

`cat` 用于输出文件内容到 Terminal

```
$ cat /etc/locale.gen     # 输出 locale.gen 的内容 
$ cat -n /etc/locale.gen  # 输出 locale.gen 的内容并显示行号
```

#### more

`more` 与 `cat` 相似，都可以查看文件内容

当一个文档太长时， `cat` 只能展示最后布满屏幕的内容，前面的内容是不可见的

这时候可用 `more` 逐行显示内容

```
$ more /etc/locale.gen
$ more +100 /etc/locale.gen       # 从 100 行开始显示
$ more -N file_name               # 默认满屏，-N 限制一屏行数
```

常用按键：

- 空格键：查看下一屏
- 回车键：往下滚动一行
- b 键：往前查看一屏
- q 键：退出

#### less

`less` 与 `more` 相似，不过 `less` 支持上下滚动查看内容，而 `more` 只支持逐行显示

```
$ less /etc/locale.gen
$ less +100 /etc/locale.gen
```

#### nano

`nano` 是一个简单实用的文本编辑器，使用简单

```
$ nano  filename       # 编辑 filename 文件，若文件不存在，则新打开一个文件，若退出时保存，则创建该文件
```

编辑完后，`ctrl + X` 提示是否保存，按 `y` 确定保存即可

**注意**：在使用过程中可用 `ctrl + G` 获取帮助

#### reboot

`reboot` 为重启命令

```
# reboot         # '$' 和 '#' 的区别在于 '$' 普通用户即可执行  
                 # 而 '#' 为 root 用户才可执行，或普通用户使用 'sudo'
```

#### poweroff

`poweroff` 为关机命令

```
# poweroff  # 马上关机
```

#### ping

`ping` 主要用于测试网络连通，通过对目标机器发送数据包来测试两台主机是否连通，及延时情况。

```
$ ping locez.com      # 通过域名 ping，若 DNS 未设置好，可能无法 ping 通
$ ping 211.157.2.94   # 通过 IP 地址 ping ，若无法 ping 通可能是网络连接出现问题
```

#### grep

Global search Regular Expression and Print out the line

可以把 `grep` 理解成字符查找工具

支持正则表达式

```
$ grep PATTERN filename      # 返回所有含有 PATTERN 的行
$ grep zh_CN /etc/locale.gen # 返回所有含 zh_CN 的行
```

[基本参数](https://www.zsythink.net/archives/1733)

#### mount

`mount` 用于挂载一个文件系统，需要 `root` 用户执行。一个磁盘可分为若干个分区，在分区上面可以创建文件系统，而挂载点则是提供一个访问的入口，将一个分区的文件系统挂载到某个目录中，称这个目录为挂载点，并且可以通过这个挂载点访问该文件系统中的内容。

例如一块硬盘在 Linux 中表示为 `/dev/sda` 那么它上面的分区应该表示为 `/dev/sda1` 、`/dev/sda2` 。

```
## mount                       # 输出系统目前的挂载信息
## mount /dev/sda1 /mnt        # 将 sda1 挂载到 /mnt 中
## cd /mnt                     # 直接通过 /mnt 访问内容
## mount -o remount,rw  /mnt   # 重新挂载 sda1 到 /mnt 并设置为 可读写 
## mount -a                    # 挂载 fstab 文件配置好的文件系统
```

#### umount

`umount` 与 `mount` 相反，是卸载一个挂载点，即取消该入口。

```
`# umount /mnt                 # 卸载 /mnt 这个挂载点的文件系统 # umount -a                   # 卸载所有已挂载的文件系统` 
```

#### tar

`tar` 主要用于创建归档文件，和解压归档文件，其本身是没有压缩功能的，但可以调用 `gzip` 、 `bzip2` 进行压缩处理。  
参数解释：

*   `-c` 创建归档
*   `-x` 解压归档
*   `-v` 显示处理过程
*   `-f` 目标文件，其后必须紧跟 目标文件
*   `-j` 调用 `bzip2` 进行解压缩
*   `-z` 调用 `gzip` 进行解压缩
*   `-t` 列出归档中的文件

```
$ tar -cvf filename.tar .       # 将当前目录所有文件归档，但不压缩，注意后面有个 ’.‘ ，不可省略，代表当前目录的意思 
$ tar -xvf filename.tar         # 解压 filename.tar 到当前文件夹
$ tar -cvjf filename.tar.bz2 .  # 使用 bzip2 压缩
$ tar -xvjf  filename.tar.bz2   # 解压 filename.tar.bz2 到当前文件夹
$ tar -cvzf filename.tar.gz     # 使用 gzip  压缩
$ tar -xvzf filename.tar.gz     # 解压 filename.tar.gz 到当前文件夹
$ tar -tf   filename            # 只查看 filename 归档中的文件，不解压
```

#### ln

`ln` 主要用于在两个文件中创建链接，链接又分为 `Hard Links` (硬链接)和 `Symbolic Links` (符号链接或软链接)，其中默认为创建**硬链接**，使用 `-s` 参数指定创建软链接。

*   硬链接主要是增加一个文件的链接数，只要该文件的链接数不为 `0` ，该文件就不会被物理删除，所以删除一个具有多个硬链接数的文件，必须删除所有它的硬链接才可删除。
*   软链接简单来说是为文件创建了一个类似快捷方式的东西，通过该链接可以访问文件，修改文件，但不会增加该文件的链接数，删除一个软链接并不会删除源文件，即使源文件被删除，软链接也存在，当重新创建一个同名的源文件，该软链接则指向新创建的文件。
*   硬链接只可链接两个文件，不可链接目录，而软链接可链接目录，所以软链接是非常灵活的。

```
$ ln source dest       # 为 source 创建一个名为 dest 的硬链接
$ ln -s source dest    # 为 source 创建一个名为 dest 的软链接
```

#### chown

`chown` 用于改变一个文件的所有者及所在的组。

```
# chown user filename        # 改变 filename 的所有者为 user
# chown user:group filename  # 改变 filename 的所有者为 user，组为 group
# chown -R root folder       # 改变 folder 文件夹及其子文件的所有者为 root
```

#### chmod

`chmod` 永远更改一个文件的权限，主要有 **读取** 、 **写入** 、 **执行** ，三种权限，其中 **所有者** 、 **用户组** 、 **其他** 各占三个，因此 `ls -l` 可以看到如下的信息

```
-rwxr--r-- 1 locez users   154 Aug 30 18:09 filename
```

其中 `r=read` ， `w=write` ， `x=execute`

```
# chmod +x filename        # 为 user ，group ，others 添加执行权限
# chmod -x filename        # 取消 user ， group ，others 的执行权限
# chmod +w filename        # 为 user 添加写入权限
# chmod ugo=rwx filename   # 设置 user ，group ，others 具有 读取、写入、执行权限
# chmod ug=rw filename     # 设置 user ，group 添加 读取、写入权限
# chmod ugo=--- filename   # 取消所有权限
```

#### useradd

`useradd` 用于添加一个普通用户。

```
# useradd -m -g users -G audio -s /usr/bin/bash newuser     
# -m 创建 home 目录， -g 所属的主组， -G 指定该用户在哪些附加组， -s 设定默认的 shell ，newuser 为新的用户名
```

#### passwd

`passwd` 用于改变用户登录密码。

```
`$ passwd    # 不带参数更改当前用户密码 # passwd newuser         # 更改上述新建的 newuser 的用户密码` 
```

#### whereis

`whereis` 用于查找文件、手册等

```
$ whereis bash 
bash: /usr/bin/bash 
      /etc/bash.bashrc 
      /etc/bash.bash_logout 
      /usr/share/man/man1/bash.1.gz 
      /usr/share/info/bash.info.gz
$ whereis -b bash       # 仅查找 binary
bash: /usr/bin/bash 
      /etc/bash.bashrc 
      /etc/bash.bash_logout
$ whereis -m bash       # 仅查找 manual
bash: /usr/share/man/man1/bash.1.gz 
      /usr/share/info/bash.info.gz
```

#### find

`find` 也用于查找文件，但更为强大，支持正则，并且可将查找结果传递到其他命令

```
$ find ./ -name PATTERN    # 从当前目录查找符合 PATTERN 的文件 
$ find /home -name PATTERN -exec ls -l {} \;  # 从 /home 文件查找所有符合 PATTERN 的文件，并交由 ls 输出详细信息
```

- `-name`
- `-type`
- `-path`
- `-mtime`

#### wget

`wget` 是一个下载工具，简单强大

```
$ wget -c url     # 下载 url 并开启断点续传
$ wget -O newname.md https://github.com/LCTT/TranslateProject/blob/master/README.md     # 下载 README 文件并重命名为 newname.md
```

#### sort

给输入排序

#### uniq

删除输入中重复的行

#### xdg-open

opens a file or URL in the user's preferred application

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