- [Bash 启动环境](#bash-启动环境)
  - [Session](#session)
      - [登录 Session](#登录-session)
      - [非登录 Session](#非登录-session)
      - [.bash\_logout](#bash_logout)
  - [启动选项](#启动选项)
  - [键盘绑定](#键盘绑定)
- [命令提示符](#命令提示符)
  - [环境变量 PS1](#环境变量-ps1)
  - [颜色](#颜色)
  - [环境变量 PS2，PS3，PS4](#环境变量-ps2ps3ps4)
- [拓展](#拓展)
  - [拓展的两种模式](#拓展的两种模式)
- [转义](#转义)
- [引号](#引号)
  - [单引号](#单引号)
  - [双引号](#双引号)
- [变量](#变量)
- [数组](#数组)
  - [创建数组](#创建数组)
  - [读取数组](#读取数组)
      - [读取单个元素](#读取单个元素)
      - [读取所有成员](#读取所有成员)
  - [数组的长度](#数组的长度)
  - [提取数组序号](#提取数组序号)
  - [提取数组成员](#提取数组成员)
  - [追加数组成员](#追加数组成员)
  - [删除数组](#删除数组)
  - [关联数组](#关联数组)
- [字符串操作](#字符串操作)
  - [字符串长度](#字符串长度)
  - [子字符串](#子字符串)
  - [搜索和替换](#搜索和替换)
      - [字符串头部的模式匹配](#字符串头部的模式匹配)
      - [字符串尾部的模式匹配](#字符串尾部的模式匹配)
      - [任意位置的模式匹配](#任意位置的模式匹配)
  - [改变大小写](#改变大小写)
- [算术运算](#算术运算)
  - [算术表达式](#算术表达式)
  - [数值的进制](#数值的进制)
  - [expr 命令](#expr-命令)
  - [let 命令](#let-命令)
- [行操作（快捷键的使用）](#行操作快捷键的使用)
  - [~~光标移动~~](#光标移动)
  - [~~编辑操作~~](#编辑操作)
  - [自动补全](#自动补全)
  - [操作历史](#操作历史)
  - [~~其他快捷键~~](#其他快捷键)

参考

- [Bash 脚本教程](https://www.ruanyifeng.com/blog/2020/04/bash-tutorial.html)

包含 shell 等词汇的解释（在第一部分）

本文的一级标题与参考文档的目录一一对应

# Bash 启动环境

## Session

用户每次使用 Shell，都会开启一个与 Shell 的 Session（对话）

Session 有两种类型：登录 Session 和非登录 Session，也可以叫做 login shell 和 non-login shell

#### 登录 Session

登录 Session 是用户登录系统以后，系统为用户开启的原始 Session，通常需要用户输入用户名和密码进行登录

登录 Session 一般进行整个系统环境的初始化，启动的初始化脚本依次如下

* `/etc/profile`：所有用户的全局配置脚本
* `/etc/profile.d`：目录里面所有 `.sh` 文件
* `~/.bash_profile`：用户的个人配置脚本
  * 如果该脚本存在，则执行完就不再往下执行
* `~/.bash_login`：如果 `~/.bash_profile` 没找到，则尝试执行这个脚本（C shell 的初始化脚本）
  * 如果该脚本存在，则执行完就不再往下执行
* `~/.profile`：如果 `~/.bash_profile` 和 `~/.bash_login` 都没找到，则尝试执行这个脚本（Bourne shell 和 Korn shell 的初始化脚本）

Linux 发行版更新的时候，会更新 `/etc` 里面的文件，比如 `/etc/profile`，因此不要直接修改这个文件

- 如果想修改所有用户的登陆环境，就在 `/etc/profile.d` 目录里面新建 `.sh` 脚本
- 如果想修改你个人的登录环境，一般是写在 `~/.bash_profile` 里面

下面是一个典型的 `.bash_profile` 文件

```bash
# .bash_profile
PATH=/sbin:/usr/sbin:/bin:/usr/bin:/usr/local/bin
PATH=$PATH:$HOME/bin

SHELL=/bin/bash
MANPATH=/usr/man:/usr/X11/man
EDITOR=/usr/bin/vi
PS1='\h:\w\$ '
PS2='> '

if [ -f ~/.bashrc ]; then
. ~/.bashrc
fi

export PATH
export EDITOR
```

可以看到，这个脚本定义了一些最基本的环境变量，然后执行了 `~/.bashrc`

参数

- `bash` 命令的 `--login` 参数，会强制执行登录 Session 会执行的脚本
- `bash` 命令的 `--noprofile` 参数，会跳过上面这些 Profile 脚本

#### 非登录 Session

非登录 Session 是用户进入系统以后，手动新建的 Session，这时不会进行环境初始化

比如，在命令行执行 `bash` 命令，就会新建一个非登录 Session

非登录 Session 的初始化脚本依次如下

*   `/etc/bash.bashrc`：对全体用户有效
*   `~/.bashrc`：仅对当前用户有效

对用户来说，`~/.bashrc` 通常是最重要的脚本

非登录 Session 默认会执行它，而登录 Session 一般也会通过调用执行它

每次新建一个 Bash 窗口，就相当于新建一个非登录 Session，所以 `~/.bashrc` 每次都会执行

注意，执行脚本相当于新建一个非互动的 Bash 环境，这种情况不会调用 `~/.bashrc`

参数

- `bash` 命令的 `--norc` 参数，可以禁止在非登录 Session 执行 `~/.bashrc` 脚本
- `bash` 命令的 `--rcfile` 参数，指定另一个脚本代替 `.bashrc`

#### .bash_logout

`~/.bash_logout` 脚本在每次退出 Session 时执行，通常用来做一些清理工作和记录工作，比如删除临时文件，记录用户在本次 Session 花费的时间

如果没有退出时要执行的命令，这个文件也可以不存在

## 启动选项

为了方便 Debug，有时在启动 Bash 的时候，可以加上启动参数

*   `-n`：不运行脚本，只检查是否有语法错误
*   `-v`：输出每一行语句运行结果前，会先输出该行语句
*   `-x`：每一个命令处理之前，先输出该命令，再执行该命令

```bash
$ bash -n scriptname
$ bash -v scriptname
$ bash -x scriptname
```

## 键盘绑定

Bash 允许用户定义自己的快捷键

全局的键盘绑定文件默认为 `/etc/inputrc`

你可以在主目录创建自己的键盘绑定文件 `.inputrc` 文件

如果定义了这个文件，需要在其中加入下面这行，保证全局绑定不会被遗漏

```bash
$include /etc/inputrc
```

`.inputrc` 文件内容如下

- `"\C-t":"pwd\n"` 表示将 `Ctrl + t` 绑定为运行 `pwd` 命令


# 命令提示符

用户进入 Bash 以后，Bash 会显示一个命令提示符，用来提示用户在该位置后面输入命令

## 环境变量 PS1

命令提示符通常是美元符号 `$`，对于根用户则是井号 `#`

这个符号是环境变量 `PS1` 决定的，执行下面的命令，可以看到当前命令提示符的定义

```bash
$ echo $PS1
```

Bash 允许用户自定义命令提示符，只要改写这个变量即可

改写后的 `PS1`，可以放在用户的 Bash 配置文件 `.bashrc` 里面，以后新建 Bash 对话时，新的提示符就会生效

要在当前窗口看到修改后的提示符，可以执行下面的命令

```bash
$ source ~/.bashrc
```

命令提示符的定义，可以包含特殊的转义字符，表示特定内容

* `\a`：响铃，计算机发出一记声音
* `\d`：以星期、月、日格式表示当前日期，例如 “Mon May 26”
* `\h`：本机的主机名
* `\H`：完整的主机名
* `\j`：运行在当前 Shell 会话的工作数
* `\l`：当前终端设备名
* `\n`：一个换行符
* `\r`：一个回车符
* `\s`：Shell 的名称
* `\t`：24小时制的`hours:minutes:seconds`格式表示当前时间
* `\T`：12小时制的当前时间
* `\@`：12小时制的`AM/PM`格式表示当前时间
* `\A`：24小时制的`hours:minutes`表示当前时间
* `\u`：当前用户名
* `\v`：Shell 的版本号
* `\V`：Shell 的版本号和发布号
* `\w`：当前的工作路径
* `\W`：当前目录名
* `\!`：当前命令在命令历史中的编号
* `\#`：当前 shell 会话中的命令数
* `\$`：普通用户显示为 `$` 字符，根用户显示为 `#` 字符
* `\[`：非打印字符序列的开始标志
* `\]`：非打印字符序列的结束标志

举例来说，`[\u@\h \W]\$` 这个提示符定义，显示出来就是

```bash
[[[email protected]](https://wangdoc.com/cdn-cgi/l/email-protection) ~]$ echo $PS1
[\u@\h \W]\$
```

改写 `PS1` 变量，就可以改变这个命令提示符

```bash
$ PS1="\A \h \$ "
17:33 host $
```

注意，`$` 后面最好跟一个空格，这样的话，用户的输入与提示符就不会连在一起

我那台机器的默认 PS1 如下

```bash
# 我并不知道是什么锤子意思

\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\u@\h:\w\$
```

## 颜色

默认情况下，命令提示符是显示终端预定义的颜色

Bash 允许自定义提示符颜色

使用下面的代码，可以设定其后文本的颜色

*   `\033[0;30m`：黑色
*   `\033[1;30m`：深灰色
*   `\033[0;31m`：红色
*   `\033[1;31m`：浅红色
*   `\033[0;32m`：绿色
*   `\033[1;32m`：浅绿色
*   `\033[0;33m`：棕色
*   `\033[1;33m`：黄色
*   `\033[0;34m`：蓝色
*   `\033[1;34m`：浅蓝色
*   `\033[0;35m`：粉红
*   `\033[1;35m`：浅粉色
*   `\033[0;36m`：青色
*   `\033[1;36m`：浅青色
*   `\033[0;37m`：浅灰色
*   `\033[1;37m`：白色

举例来说，如果要将提示符设为红色，可以将 `PS1` 设成下面的代码

```bash
PS1='\[\033[0;31m\]<\u@\h \W>\$ '
```

但是，上面这样设置以后，用户在提示符后面输入的文本也是红色的

为了解决这个问题，可以在结尾添加另一个特殊代码 `\[\033[00m\]`，表示将其后的文本恢复到默认颜色

```bash
PS1='\[\033[0;31m\]<\u@\h \W>\$\[\033[00m\] '
```

除了设置前景颜色，Bash 还允许设置背景颜色

*   `\033[0;40m`：蓝色
*   `\033[1;44m`：黑色
*   `\033[0;41m`：红色
*   `\033[1;45m`：粉红
*   `\033[0;42m`：绿色
*   `\033[1;46m`：青色
*   `\033[0;43m`：棕色
*   `\033[1;47m`：浅灰色

下面是一个带有红色背景的提示符

```bash
PS1='\[\033[0;41m\]<\u@\h \W>\$\[\033[0m\] '
```

## 环境变量 PS2，PS3，PS4

除了 `PS1`，Bash 还提供了提示符相关的另外三个环境变量

环境变量 `PS2` 默认为 `>`

- 是命令行折行输入时系统的提示符
- 下面命令中，输入 `hello` 以后按下回车键，系统会提示继续输入
- 这时，第二行显示的提示符就是 `PS2` 定义的 `>`

```bash
$ echo "hello
> world"
```

环境变量 `PS3`

- 使用 `select` 命令时系统输入菜单的提示符

环境变量 `PS4` 默认为 `+`

- 它是使用 Bash 的 `-x` 参数执行脚本时，那个提示符


# 拓展

Bash 提供 8 种 拓展 ( globbing )

- `~` 扩展
- `?` 字符扩展
- `*` 字符扩展
- `[]` 扩展
- `{}` 扩展
- 变量扩展
  - `${!sth*}` 扩展成所有以 sth 开头的变量名
- 子命令扩展
  - `$(..)` 可以扩展成另一个命令的运行结果，该命令的所有输出都会作为返回值
- 算术扩展
  - `$((..))` 可以扩展成整数运算的结果，详见《Bash 的算术运算》一章

文件名包括特殊字符时（如 `*`），引用文件名，需要把文件名放在单引号或双引号里面

```bash
$ touch 'fo*'  # 创建名字为 fo* 的文件
```

> Shell 接收到用户输入的命令后，会根据空格将用户的输入拆分成一个个词元（token）  
> 
> Shell 先会扩展词元里面的特殊字符，扩展完成后才会调用相应的命令

## 拓展的两种模式

- 总是拓展
- 匹配失败，原样输出

目录下没有 `a.txt b.txt`

![](image/2023-06-27-17-39-27.png)

# 转义

> Bash 只有一种数据类型，就是字符串

某些字符在 Bash 里面有特殊含义（比如 `$ & *`）

如果想要原样输出这些特殊字符，就必须在它们前面加上反斜杠，使其变成普通字符

这就叫做 “转义”（escape）

反斜杠除了用于转义，还可以表示一些不可打印的字符

- `\a`：响铃
- `\b`：退格
- `\n`：换行
- `\r`：回车
- `\t`：制表符

```bash
# \ 的另一个用法

$ mv \
/path/to/foo \
/path/to/bar

# 等同于
$ mv /path/to/foo /path/to/bar
```

# 引号

## 单引号

单引号用于保留字符的字面含义，==各种特殊字符==在单引号里面，都会变为普通字符

打印单引号的方法

```
$ echo "it's"
it's
```

## 双引号

双引号比单引号宽松，==大部分特殊字符==在双引号里面，会失去特殊含义，变成普通字符

`` $ `` `` ` ``  `` \ `` 这三个仍然有特殊函数

用法
- 使用反斜杠，在双引号之中插入双引号，或者插入反斜杠
- `$ ls "two words.txt"` 引用有空格的文件名
- `$ echo "this is a     test"` 保留多个空格
- 保存原始命令的输出格式

```bash
# 单行输出
$ echo $(cal)
一月 2020 日 一 二 三 四 五 六 1 2 3 ... 31

# 原始格式输出
$ echo "$(cal)"
      一月 2020
日 一 二 三 四 五 六
          1  2  3  4
 5  6  7  8  9 10 11
12 13 14 15 16 17 18
19 20 21 22 23 24 25
26 27 28 29 30 31
```

# 变量

> Bash 变量分成环境变量和自定义变量两类

- 常见的**环境变量**
- 自定义变量
  - 创建
  - 读取
  - 删除：`unset SOME`
  - 输出：`export SOME='sth'` 
    - 当前 Shell 及随后新建的子 Shell，都可以读取变量 `$SOME`
    - 子 Shell 如果修改继承的变量，不会影响父 Shell  
- 特殊变量
  - 这些变量的值由 Shell 提供，用户不能进行赋值，详见[参考文档：变量]()一节
- 对变量的其他操作
  - 变量的默认值 `${SOME op return}`
  - `declare`
  - `readonly`
  - `let`


# 数组

数组（array）是一个包含多个值的变量

## 创建数组

数组可以采用逐个赋值的方法创建

```bash
ARRAY[INDEX]=value
```

- `ARRAY` 是数组的名字
- `INDEX` 是一个大于或等于零的整数，也可以是算术表达式
- 注意数组第一个元素的下标是 0

数组也可以采用一次性赋值的方式创建

```bash
ARRAY=(value1 value2 ... valueN)

# 等同于

ARRAY=(
  value1
  value2
  value3
)

# 也可以在每个值前面指定位置

$ array=(a b c)
$ array=([2]=c [0]=a [1]=b)

$ days=(Sun Mon Tue Wed Thu Fri Sat)
$ days=([0]=Sun [1]=Mon [2]=Tue [3]=Wed [4]=Thu [5]=Fri [6]=Sat)

# hatter 是数组的 0 号位置，duchess 是 5 号位置，alice 是6号位置
# 没有赋值的数组元素的默认值是空字符串

names=(hatter [5]=duchess alice)
```

定义数组的时候，可以使用通配符

```bash
# 将当前目录的所有 MP3 文件，放进一个数组

$ mp3s=( *.mp3 )
```

数组的其他声明赋值方式

- `declare -a ARRAYNAME` 命令声明一个数组 ARRAYNAME
- `read -a dice` 命令则是将用户的命令行输入，存入数组 `dice`

## 读取数组

#### 读取单个元素

读取数组指定位置的成员，要使用下面的语法

```bash
$ echo ${array[i]}     # i 是索引
```

上面语法里面的大括号是必不可少的，否则 Bash 会把索引部分 `[i]` 按照原样输出

```bash
$ array[0]=fuck

$ echo ${array[0]}
fuck

$ echo $array[0]
fuck[0]

# $array 等价 ${array[0]}
```

#### 读取所有成员

`@` 和 `*` 是数组的特殊索引，表示返回数组的所有成员

```bash
$ foo=(a b c d e f)
$ echo ${foo[@]}
a b c d e f
```
这两个特殊索引配合 `for` 循环，就可以用来遍历数组

```bash
for i in "${names[@]}"; do
  echo $i
done
```

`@` 放不放在双引号之中，是有差别的

```bash
$ activities=( swimming "water skiing" canoeing "white-water rafting" surfing )
$ for act in ${activities[@]}; \
do \
echo "Activity: $act"; \
done

Activity: swimming
Activity: water
Activity: skiing
Activity: canoeing
Activity: white-water
Activity: rafting
Activity: surfing
```

- 上面的例子中，数组 `activities` 实际包含 5 个成员
- 但是 `for...in` 循环直接遍历 `${activities[@]}`，导致返回 7 个结果
- 为了避免这种情况，一般把 `${activities[@]}` 放在双引号之中

`${activities[*]}` 不放在双引号之中，跟 `${activities[@]}` 不放在双引号之中是一样的

`${activities[*]}` 放在双引号之中，所有成员都会串起来

```bash
$ for act in "${activities[*]}"; \
do \
echo "Activity: $act"; \
done

Activity: swimming water skiing canoeing white-water rafting surfing
```

拷贝一个数组的最方便方法，就是写成下面这样

```bash
# activities 拷贝给 hobbies

$ hobbies=( "${activities[@]}" )
```

```bash
# hobbies 在数组 activities 的所有成员之后，又添加了一个成员 diving

$ hobbies=( "${activities[@]}" diving )
```

## 数组的长度

要想知道数组的长度（即一共包含多少成员），可以使用下面两种语法

```
${#array[*]}
${#array[@]}
```

下面是一个例子

```bash
# 把字符串赋值给 100 位置的数组元素，这时的数组只有一个元素

$ a[100]=foo

$ echo ${#a[*]}
1

$ echo ${#a[@]}
1
```

注意，如果用这种语法去读取具体的数组成员，就会返回该成员的字符串长度。这一点必须小心。

```bash
$ a[100]=foo
$ echo ${#a[100]}
3
```

## 提取数组序号

`${!array[@]}` 或 `${!array[*]}`，可以返回数组的成员序号，即哪些位置是有值的

```bash
$ arr=([5]=a [9]=b [23]=c)
$ echo ${!arr[@]}
5 9 23
$ echo ${!arr[*]}
5 9 23
```

利用这个语法，也可以通过 `for` 循环遍历数组

```bash
arr=(a b c d)

for i in ${!arr[@]};do
  echo ${arr[i]}
done
```

## 提取数组成员

`${array[@]:position:length}` 的语法可以提取数组成员

```bash
$ food=( apples bananas cucumbers dates eggs fajitas grapes )
$ echo ${food[@]:1:1}
bananas
$ echo ${food[@]:1:3}
bananas cucumbers dates
```

如果省略长度参数 `length`，则返回从指定位置开始的所有成员

## 追加数组成员

数组末尾追加成员，可以使用 `+=` 赋值运算符

它能够自动地把值追加到数组末尾

```bash
$ foo=(a b c)
$ echo ${foo[@]}
a b c

$ foo+=(d e f)
$ echo ${foo[@]}
a b c d e f
```

## 删除数组

删除一个数组成员，使用 `unset` 命令

```bash
$ unset foo[2]
```

将某个成员设为空值，可以从返回值中 “隐藏” 这个成员

```bash
$ foo=(a b c d e f)
$ foo[1]=''            # $ foo[1]=  不建议这种写法
$ echo ${#foo[@]}
6
$ echo ${!foo[@]}
0 1 2 3 4 5
```

注意，这里是 “隐藏”，而不是删除，因为这个成员仍然存在，只是值变成了空值

`unset ArrayName` 可以清空整个数组

## 关联数组

Bash 的新版本支持关联数组

关联数组使用字符串而不是整数作为数组索引

`declare -A` 可以声明关联数组

```bash
declare -A colors
colors["red"]="#ff0000"
colors["green"]="#00ff00"
colors["blue"]="#0000ff"
```

关联数组必须用带有 `-A` 选项的 `declare` 命令声明创建

访问关联数组成员的方式，几乎与整数索引数组相同

```bash
echo ${colors["blue"]}
```


# 字符串操作

## 字符串长度

获取字符串长度的语法 `${#varname}`

## 子字符串

字符串提取子串的语法

```
$ varname=frogmanfoot
$ echo ${varname:4:3}
man
```

## 搜索和替换

#### 字符串头部的模式匹配

```bash
# 原始变量不会发生变化

# 如果 pattern 匹配变量 variable 的开头，
# 删除最短匹配（非贪婪匹配）的部分，返回剩余部分
${variable#pattern}

# 如果 pattern 匹配变量 variable 的开头，
# 删除最长匹配（贪婪匹配）的部分，返回剩余部分
${variable##pattern}

# 将头部匹配的部分，替换成其他内容
# 模式必须出现在字符串的开头
${variable/#pattern/string}
```

#### 字符串尾部的模式匹配

```bash
# 对称于上面的用法
${variable%pattern}

${variable%%pattern}

${variable/%pattern/string}
```

#### 任意位置的模式匹配

```bash
# 最长匹配的那部分被 string 替换，但仅替换第一个匹配
${variable/pattern/string}

# 最长匹配的那部分被 string 替换，所有匹配都替换
${variable//pattern/string}

# 模式部分可以使用通配符
```

## 改变大小写

```bash
# 转为大写
${varname^^}

# 转为小写
${varname,,}
```

# 算术运算

## 算术表达式

`((...))`

如果要读取结果，前面加上美元符号 `$`

支持的操作符

- 算术运算
- 位运算
- 逻辑运算
- 赋值运算
  - `$((a=1))`
  - 上面例子中，`a=1` 对变量 a 进行赋值
  - 这个式子本身也是一个表达式，返回值就是等号右边的值

## 数值的进制

- `number`：没有任何特殊表示法的数字是十进制数
- `0number`：八进制数
- `0xnumber`：十六进制数
- `base#number`：base 进制的数

## expr 命令

`expr` 命令支持**算术运算**

## let 命令

`let` 命令用于将**算术运算**的结果，赋予一个变量

# 行操作（快捷键的使用）

Bash 内置了 Readline 库，具有这个库提供的很多 “行操作” 功能

- 默认采用 Emacs 快捷键，下文都是 Emacs 风格的快捷键
- 还支持 Vim 风格

```bash
$ set -o vi     # 改成 Vi 快捷键
```

可以在主目录中的 `~/.bashrc` 文件底部添加下面的行来持久设置它 `set -o vi`

## ~~光标移动~~
- `Ctrl + b`：向行首移动一个字符，与左箭头作用相同
- `Ctrl + f`：向行尾移动一个字符，与右箭头作用相同
- `Alt + b`：移动到当前单词的词首
- `Alt + f`：移动到当前单词的词尾
- `Ctrl + a`：移到行首
- `Ctrl + e`：移到行尾

上面快捷键的 Alt 键，也可以用 ESC 键代替

## ~~编辑操作~~

下面的快捷键可以编辑命令行内容。

- `Ctrl + d`：删除光标位置的字符（delete）
- `Ctrl + w`：删除光标前面的单词
- `Alt + l`：将光标位置至词尾转为小写（lowercase）
- `Alt + u`：将光标位置至词尾转为大写（uppercase）

使用 `Ctrl + d` 的时候，如果当前行没有任何字符，会导致退出当前 Shell

剪切和粘贴快捷键如下

- `Ctrl + u`：剪切光标位置到行首的文本
- `Ctrl + k`：剪切光标位置到行尾的文本
- `Alt + d`：剪切光标位置到词尾的文本
- `Alt + Backspace`：剪切光标位置到词首的文本
- `Ctrl + y`：在光标位置粘贴文本

Alt 键可以用 Esc 键代替

## 自动补全

命令输入到一半的时候，可以按一下 Tab 键，Readline 会自动补全==命令或路径==

- 比如，输入 cle ，再按下 Tab 键，Bash 会自动将这个命令补全为 clear

如果符合条件的命令或路径有多个，就需要连续按两次 Tab 键，Bash 会提示所有符合条件的命令或路径

一些快捷键见[参考文档]()

## 操作历史

```bash
$ echo $HISTFILE
/home/me/.bash_history
```

`history`
- 退出当前 Shell 的时候，Bash 会将用户在当前 Shell 的操作历史写入 `~/.bash_history` 文件，该文件默认储存 500 个操作
- 环境变量 `HISTFILE` 总是指向这个文件
- 更多 `history` 的配置见[参考文档]()

`!sth`
- 只会匹配命令，不会匹配参数
- 打印 `!` 记得转义
- 开启选项 `histverify`，`!` 的拓展会先打印出来，等到用户按下回车键后再执行
- `!!`：执行上一个命令
- `!number`：历史文件里面行号为 number 的命令
- `!string`：执行最近一个以指定字符串 string 开头的命令
- `!?string`：执行最近一条包含字符串 string 的命令

`^r`

## ~~其他快捷键~~

- `Ctrl + j`：等同于回车键（LINEFEED）
- `Ctrl + m`：等同于回车键（CARRIAGE RETURN）
- `Ctrl + [`：等同于 ESC


