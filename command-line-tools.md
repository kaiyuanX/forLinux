- [如何寻求帮助？](#如何寻求帮助)
  - [man](#man)
  - [tldr](#tldr)
  - [info](#info)
  - [help](#help)
- [目录堆栈](#目录堆栈)
  - [ls](#ls)
  - [pwd](#pwd)
  - [cd](#cd)
  - [pushd，popd](#pushdpopd)
  - [dirs](#dirs)
- [文件管理](#文件管理)
  - [mkdir](#mkdir)
  - [rm](#rm)
  - [cp](#cp)
  - [mv](#mv)
  - [touch](#touch)
- [输入输出控制](#输入输出控制)
  - [重定向](#重定向)
  - [管道](#管道)
  - [xargs](#xargs)
  - [tee](#tee)
- [文本处理](#文本处理)
  - [vim](#vim)
  - [grep](#grep)
  - [sed](#sed)
  - [awk](#awk)
  - [wc](#wc)
  - [sort](#sort)
  - [uniq](#uniq)
  - [bc](#bc)
- [文件检索](#文件检索)
  - [more](#more)
  - [less](#less)
  - [whereis](#whereis)
  - [find](#find)
- [系统网络](#系统网络)
  - [reboot](#reboot)
  - [poweroff](#poweroff)
  - [ping](#ping)
- [权限用户](#权限用户)
  - [chown](#chown)
  - [chmod](#chmod)
  - [useradd](#useradd)
  - [passwd](#passwd)
- [其他](#其他)
  - [wget](#wget)
  - [tmux](#tmux)
  - [xdg-open](#xdg-open)
  - [basename](#basename)
  - [read 命令](#read-命令)
      - [用法](#用法)
      - [参数](#参数)
      - [IFS 变量](#ifs-变量)
- [暂未接触 ](#暂未接触)
  - [mount](#mount)
  - [umount](#umount)
  - [tar](#tar)
  - [ln](#ln)

参考
- [新手指南： Linux 新手应该知道的 26 个命令](https://linux.cn/article-6160-1.html)
- [南京大学 计算机系统基础 课程实验 2021](https://nju-projectn.github.io/ics-pa-gitbook/ics2021/linux.html)

# 如何寻求帮助？

## man

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

## tldr

tldr pages: Too Long; Didn't Read

它简化了烦琐的 man pages，仅列出某指令常用的使用方法

## info

与 `man` 不同的是，可以像浏览网页一样在各个节点中跳转。

从文档首页开始浏览

```
$ info
```

获取特定程序的帮助

```
$ info program
```

## help

除了上面的两种方法外，还有一种简单使用的方法，那就是 `--help` 参数，一般程序都会有这个参数，会输出最简单有用的介绍

```
$ man --help       # 获取 man 的帮助
```


# 目录堆栈

方便用户在不同目录之间切换

参考

- [Bash 脚本教程](https://www.ruanyifeng.com/blog/2020/04/bash-tutorial.html)

## ls

( list )

```
$ ls       
$ ls -l    # 可见文件详细信息
$ ls -hl   # 可见文件详细信息并以可读大小显示文件大小
$ ls -al   # 所有文件（包括隐藏）的详细信息
```

**注意**： Linux 中 以 `.` 开头的文件或文件夹均为隐藏文件或隐藏文件夹

## pwd

`pwd` 用于返回当前工作目录的绝对路径

print working directory

## cd

`cd` 是打开某个路径的命令，也就是打开某个文件夹，并跳转到该处

```
$ cd path      # path 为你要打开的路径。
```

其中 path 有绝对路径和相对路径之分

绝对路径强调从 `/` 起，一直到所在路径

另外在 Linux 中， `.` 代表当前目录， `..` 代表上级目录，因此返回上级目录 `cd ..`

`cd -`

## pushd，popd

```bash
# 当前处在主目录，堆栈为空
$ pwd
/home/me

# 进入 /home/me/foo
# 当前堆栈为 /home/me
$ pushd ~/foo

# 进入 /etc
# 当前堆栈为 /home/me/foo /home/me
$ pushd /etc

# 进入 /home/me/foo
# 当前堆栈为 /home/me
$ popd

# 进入 /home/me
# 当前堆栈为
$ popd

# 目录不变，当前堆栈为空
$ popd
```

## dirs

显示目录堆栈的内容，目录之间使用空格分隔

第一个为当前所在位置，第二个是栈顶，以此类推

`-c`：清空目录栈


# 文件管理

## mkdir

`mkdir` 用于新建文件夹

```
$ mkdir folder
$ mkdir -p folder/subfolder    # -p ：当父目录不存在时则建立，用此参数可建立多级文件夹
```

## rm

`rm` 删除文件 ( remove ) 

```
$ rm filename      # 删除 filename
$ rm -i filename   # 删除 filename 前提示，若多个文件则每次提示
$ rm -rf folder/subfolder/  # 递归删除 subfolder 下所有文件及文件夹，包括 subfolder 自身
$ rm -d folder     #  删除空文件夹
```

## cp

`cp` 复制文件 ( copy )

```
$ cp source dest            # 将 source 复制到 dest
$ cp folder/*  dest         # 将 folder 下所有文件(不含子文件夹中的文件)复制到 dest
$ cp -r folder  dest        # 将 folder 下所有文件（包含子文件夹中的所有文件）复制到 dest
```

## mv

`mv` 移动文件 ( move )

```
$ mv source folder         # 将 source 移动到 folder 下，完成后则为  folder/source
$ mv source dest           # 在 dest 不为目录的前提下，重命名 source 为 dest
$ mv -i source folder      # 在移动时，若文件已存在则提示 **是否覆盖** 
```

## touch

可以创建一个内容为空的新文件NEWFILE, 若NEWFILE已存在, 其内容不会丢失

```
$ touch NEWFILE
```


# 输入输出控制

## 重定向

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

## 管道

`|`

## xargs

把 input 转化为 initial-arguments 传给 command

`xargs [options] [command [initial-arguments]]`

## tee

read from input and write to standard output and files

# 文本处理

## vim

详见 [tools-vim-ms](tools-vim-ms.md)

## grep

Global search Regular Expression and Print out the line

可以把 `grep` 理解成字符查找工具

支持正则表达式

```
$ grep PATTERN filename      # 返回所有含有 PATTERN 的行
$ grep zh_CN /etc/locale.gen # 返回所有含 zh_CN 的行
```

[基本参数](https://www.zsythink.net/archives/1733)

## sed

专注于处理行

```
# 对每行进行匹配替换
# g 表每行的全部匹配都替换

command | sed 's/<oldWord>/<newWord>/g'  
```

## awk

专注于处理列

- `$0` 表示整行的内容
- `$1` 到 `$n` 为一行中的 n 个区域

区域的分割基于 `awk` 的域分隔符（默认是空格，可以通过 `-F` 来修改）

例子：让我们统计一下所有以 `c` 开头，以 `e` 结尾，并且仅尝试过一次登录的用户

输入

```bash
12 username1
45 xky
1 fuck
1 jj
3 uu
```

```bash
# 通过管道传给 awk
| awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l
```

其中 `$1 == 1 && $2 ~ /^c[^ ]*e$/` 为匹配要求

- 第一部分需要等于 1，
- 第二部分必须满足给定的一个正则表达式

代码块 `{ print $2 }`

- 表示打印第二部分

然后我们使用 `wc -l` 统计输出结果的行数

## wc

word count

## sort

给输入排序

```bash
# 使用SEP作为列的分隔符
-t SEP

# k 后面这一串给出排序标准的位置和类型
-k FStart.CStart Modifier,FEnd.CEnd Modifier
```

[Linux sort 命令详解](https://wangchujiang.com/linux-command/c/sort.html)

## uniq

删除输入中重复的行

`-c` 额外打印出重复行的数量

## bc 

An arbitrary precision calculator


# 文件检索

## more

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

## less

`less` 与 `more` 相似，不过 `less` 支持上下滚动查看内容，而 `more` 只支持逐行显示

```
$ less /etc/locale.gen
$ less +100 /etc/locale.gen
```

## whereis

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

## find

`find` 也用于查找文件，但更为强大，支持正则，并且可将查找结果传递到其他命令

```
$ find ./ -name PATTERN    # 从当前目录查找符合 PATTERN 的文件 
$ find /home -name PATTERN -exec ls -l {} \;  # 从 /home 文件查找所有符合 PATTERN 的文件，并交由 ls 输出详细信息
```

- `-name`
- `-type`
- `-path`
- `-mtime`


# 系统网络

## reboot

`reboot` 为重启命令

```
# reboot         # '$' 和 '#' 的区别在于 '$' 普通用户即可执行  
                 # 而 '#' 为 root 用户才可执行，或普通用户使用 'sudo'
```

## poweroff

`poweroff` 为关机命令

```
# poweroff  # 马上关机
```

## ping

`ping` 主要用于测试网络连通，通过对目标机器发送数据包来测试两台主机是否连通，及延时情况。

```
$ ping locez.com      # 通过域名 ping，若 DNS 未设置好，可能无法 ping 通
$ ping 211.157.2.94   # 通过 IP 地址 ping ，若无法 ping 通可能是网络连接出现问题
```


# 权限用户

## chown

`chown` 用于改变一个文件的所有者及所在的组

```
# chown user filename        # 改变 filename 的所有者为 user
# chown user:group filename  # 改变 filename 的所有者为 user，组为 group
# chown -R root folder       # 改变 folder 文件夹及其子文件的所有者为 root
```

## chmod

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

## useradd

`useradd` 用于添加一个普通用户

```
# useradd -m -g users -G audio -s /usr/bin/bash newuser     
# -m 创建 home 目录， -g 所属的主组， -G 指定该用户在哪些附加组， -s 设定默认的 shell ，newuser 为新的用户名
```

## passwd

`passwd` 用于改变用户登录密码

```
`$ passwd    # 不带参数更改当前用户密码 # passwd newuser         # 更改上述新建的 newuser 的用户密码` 
```


# 其他

## wget

`wget` 是一个下载工具，简单强大

```
$ wget -c url     # 下载 url 并开启断点续传
$ wget -O newname.md https://github.com/LCTT/TranslateProject/blob/master/README.md     # 下载 README 文件并重命名为 newname.md
```

## tmux

见 [tools-tmux](tools-tmux.md)

## xdg-open

opens a file or URL in the user's preferred application

## basename

- 从文件的完整路径获取文件名
- 用于删除文件的扩展名

[basename命令删除扩展名](https://www.myfreax.com/basename-command-in-linux/)

## read 命令

它将用户的输入存入一个变量，方便后面的代码使用

参考

- [Bash 脚本教程](https://www.ruanyifeng.com/blog/2020/04/bash-tutorial.html)

#### 用法

用户按下回车键，就表示输入结束

`read` 命令的格式如下

```bash
read [-options] [variable...]
```

- `options` 是参数选项
- `variable` 是用来保存输入数值的一个或多个变量名
  - 如果没有提供变量名，环境变量 `REPLY` 会包含用户输入的一整行数据

下面是一个例子 `demo.sh`

```
#!/bin/bash

echo -n "输入一些文本 > "
read text
echo "你的输入：$text"
```


```
$ bash demo.sh
输入一些文本 > 你好，世界
你的输入：你好，世界
```

`read` 可以接受用户输入的多个值

- 如果用户的输入项少于 `read` 命令给出的变量数目，那么额外的变量值为空
- 如果用户的输入项多于定义的变量，那么多余的输入项会包含到最后一个变量中
- 如果 `read` 命令之后没有定义变量名，那么环境变量`REPLY`会包含所有的输入

```
#!/bin/bash
# read-single: read multiple values into default variable

echo -n "Enter one or more values > "
read
echo "REPLY = '$REPLY'"
```

运行结果如下

```
$ read-single
Enter one or more values > a b c d
REPLY = 'a b c d'
```

`read` 命令除了读取键盘输入，可以用来读取文件

```
#!/bin/bash

filename='/etc/hosts'

while read myline
do
  echo "$myline"
done < $filename
```

上面的例子通过 `read` 命令，读取一个文件的内容

`done` 命令后面的定向符 `<`，将文件内容导向 `read` 命令，每次读取一行，存入变量`myline`，直到文件读取完毕

#### 参数

`read` 命令的参数如下

1. `-t` 参数

设置了超时的秒数

如果超过了指定时间，用户仍然没有输入，脚本将放弃等待，继续向下执行

```
#!/bin/bash

echo -n "输入一些文本 > "
if read -t 3 response; then
  echo "$response"
else
  echo "用户没有输入"
fi
```

环境变量 `TMOUT` 也可以起到同样作用，指定 `read` 命令等待用户输入的时间（单位为秒）

```bash
$ TMOUT=3
$ read response
```

上面例子也是等待3秒，如果用户还没有输入，就会超时

2. `-p` 参数

指定用户输入的提示信息

```bash
read -p "Enter one or more values > "
echo "REPLY = '$REPLY'"
```

上面例子中，先显示`Enter one or more values >`，再接受用户的输入。

3. `-a` 参数

把用户的输入赋值给一个数组，从零号位置开始

```bash
$ read -a people
alice duchess dodo
$ echo ${people[2]}
dodo
```

4. `-n` 参数

指定只读取若干个字符作为变量值，而不是整行读取

```
$ read -n 3 letter
abcdefghij
$ echo $letter
abc
```

5. `-e` 参数

允许用户输入的时候，使用 `readline` 库提供的快捷键

6. 其他参数

*   `-d delimiter`：定义字符串 `delimiter` 的第一个字符作为用户输入的结束，而不是一个换行符
*   `-r`：raw 模式，表示不把用户输入的反斜杠字符解释为转义字符
*   `-s`：使得用户的输入不显示在屏幕上，这常常用于输入密码或保密信息
*   `-u fd`：使用文件描述符 `fd` 作为输入

#### IFS 变量

`read` 命令读取的值，默认是以空格分隔

可以通过自定义环境变量 `IFS`（内部字段分隔符，Internal Field Separator 的缩写），修改分隔标志

`IFS` 的默认值是空格、Tab 符号、换行符号，通常取第一个（即空格）

如果把 `IFS` 定义成冒号（`:`）或分号（`;`），就可以分隔以这两个符号分隔的值，这对读取文件很有用

```
#!/bin/bash
# read-ifs: read fields from a file

FILE=/etc/passwd

read -p "Enter a username > " user_name
file_info="$(grep "^$user_name:" $FILE)" // 把命令 grep 的结果传给 file_info

if [ -n "$file_info" ]; then
  IFS=":" read user pw uid gid name home shell <<< "$file_info"
  echo "User = '$user'"
  echo "UID = '$uid'"
  echo "GID = '$gid'"
  echo "Full Name = '$name'"
  echo "Home Dir. = '$home'"
  echo "Shell = '$shell'"
else
  echo "No such user '$user_name'" >&2
  exit 1
fi
```

上面例子中，`IFS` 设为冒号，然后用来分解 `/etc/passwd` 文件的一行

**`IFS` 的赋值命令和 `read` 命令写在一行**
- `IFS` 的改变仅对后面的命令生效
- 该命令执行后 `IFS` 会自动恢复原来的值

等价下面的写法

```
OLD_IFS="$IFS"
IFS=":"
read user pw uid gid name home shell <<< "$file_info"
IFS="$OLD_IFS"
```

另外，上面例子中，`<<<` 是 here-string，用于**将变量值转为标准输入**

如果 `IFS` 设为空字符串，就等同于将整行读入一个变量

```
#!/bin/bash
input="/path/to/txt/file"
while IFS= read -r line
do
  echo "$line"
done < "$input"
```

上面的命令可以逐行读取文件，每一行存入变量 `line`，打印出来以后再读取下一行


# 暂未接触 

以下命令按照通常的使用频度排列

## mount

`mount` 用于挂载一个文件系统，需要 `root` 用户执行。一个磁盘可分为若干个分区，在分区上面可以创建文件系统，而挂载点则是提供一个访问的入口，将一个分区的文件系统挂载到某个目录中，称这个目录为挂载点，并且可以通过这个挂载点访问该文件系统中的内容。

例如一块硬盘在 Linux 中表示为 `/dev/sda` 那么它上面的分区应该表示为 `/dev/sda1` 、`/dev/sda2` 。

```
# mount                       # 输出系统目前的挂载信息
# mount /dev/sda1 /mnt        # 将 sda1 挂载到 /mnt 中
# cd /mnt                     # 直接通过 /mnt 访问内容
# mount -o remount,rw  /mnt   # 重新挂载 sda1 到 /mnt 并设置为 可读写 
# mount -a                    # 挂载 fstab 文件配置好的文件系统
```

## umount

`umount` 与 `mount` 相反，是卸载一个挂载点，即取消该入口。

```
`# umount /mnt                 # 卸载 /mnt 这个挂载点的文件系统 # umount -a                   # 卸载所有已挂载的文件系统` 
```

## tar

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

## ln

`ln` 主要用于在两个文件中创建链接，链接又分为 `Hard Links` (硬链接)和 `Symbolic Links` (符号链接或软链接)，其中默认为创建**硬链接**，使用 `-s` 参数指定创建软链接。

*   硬链接主要是增加一个文件的链接数，只要该文件的链接数不为 `0` ，该文件就不会被物理删除，所以删除一个具有多个硬链接数的文件，必须删除所有它的硬链接才可删除。
*   软链接简单来说是为文件创建了一个类似快捷方式的东西，通过该链接可以访问文件，修改文件，但不会增加该文件的链接数，删除一个软链接并不会删除源文件，即使源文件被删除，软链接也存在，当重新创建一个同名的源文件，该软链接则指向新创建的文件。
*   硬链接只可链接两个文件，不可链接目录，而软链接可链接目录，所以软链接是非常灵活的。

```
$ ln source dest       # 为 source 创建一个名为 dest 的硬链接
$ ln -s source dest    # 为 source 创建一个名为 dest 的软链接
```