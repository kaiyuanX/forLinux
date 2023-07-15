- [Bash 脚本入门](#bash-脚本入门)
  - [Shebang 行](#shebang-行)
  - [执行权限和路径](#执行权限和路径)
  - [env 命令](#env-命令)
  - [注释](#注释)
  - [脚本参数](#脚本参数)
  - [shift 命令](#shift-命令)
  - [getopts 命令](#getopts-命令)
  - [配置项参数终止符 --](#配置项参数终止符---)
  - [exit 命令](#exit-命令)
  - [命令执行结果](#命令执行结果)
  - [source 命令](#source-命令)
  - [别名，alias 命令](#别名alias-命令)
- [判断表达式 condition](#判断表达式-condition)
  - [test 命令](#test-命令)
  - [文件判断](#文件判断)
  - [字符串判断](#字符串判断)
  - [整数判断](#整数判断)
  - [正则判断](#正则判断)
  - [算术判断](#算术判断)
  - [test 判断的逻辑运算](#test-判断的逻辑运算)
  - [普通命令的逻辑运算](#普通命令的逻辑运算)
- [分支](#分支)
  - [if 结构](#if-结构)
  - [case 结构](#case-结构)
- [循环](#循环)
  - [while 循环](#while-循环)
  - [until 循环](#until-循环)
  - [for...in 循环](#forin-循环)
  - [for 循环](#for-循环)
  - [break，continue](#breakcontinue)
  - [select 结构](#select-结构)
- [Bash 函数](#bash-函数)
  - [简介](#简介)
  - [参数变量](#参数变量)
  - [return 命令](#return-命令)
  - [全局变量和局部变量，local 命令](#全局变量和局部变量local-命令)
- [脚本除错](#脚本除错)
  - [常见错误](#常见错误)
  - [用于除错的环境变量](#用于除错的环境变量)
      - [LINENO](#lineno)
      - [FUNCNAME](#funcname)
      - [BASH\_SOURCE](#bash_source)
      - [BASH\_LINENO](#bash_lineno)
- [set 命令，shopt 命令](#set-命令shopt-命令)
  - [简介](#简介-1)
  - [set -u](#set--u)
  - [set -x](#set--x)
  - [脚本的错误处理](#脚本的错误处理)
  - [set -e](#set--e)
  - [set -o pipefail](#set--o-pipefail)
  - [set -E ??](#set--e-)
  - [其他参数](#其他参数)
  - [set 参数的组合使用](#set-参数的组合使用)
  - [shopt 命令](#shopt-命令)
- [mktemp 命令，trap 命令](#mktemp-命令trap-命令)
  - [临时文件的安全问题](#临时文件的安全问题)
  - [mktemp 命令的用法](#mktemp-命令的用法)
  - [mktemp 命令的参数](#mktemp-命令的参数)
  - [trap 命令的用法](#trap-命令的用法)


参考

- [Bash 脚本教程](https://www.ruanyifeng.com/blog/2020/04/bash-tutorial.html)


本文的一级标题与参考文档的目录一一对应

# Bash 脚本入门

## Shebang 行

脚本的第一行通常是指定解释器

这一行以 `#!` 字符开头，这个字符称为 Shebang，所以这一行就叫做 Shebang 行

`#!` 后面就是脚本解释器的位置，Bash 脚本的解释器一般是 `/bin/sh` 或 `/bin/bash`

`#!` 与脚本解释器之间有没有空格，都是可以的

```c
#!/bin/sh
# 或者
#!/bin/bash
```


如果 Bash 解释器不放在目录 `/bin`，脚本就无法执行了。为了保险，可以写成下面这样

```
#!/usr/bin/env bash
```

上面命令使用 `env` 命令（这个命令总是在`/usr/bin`目录），返回 Bash 可执行文件的位置

`env` 命令的详细介绍，请看后文

Shebang 行不是必需的，但是建议加上这行。如果缺少该行，就需要手动将脚本传给解释器

举例来说，脚本是 `script.sh`，有 Shebang 行的时候，可以直接调用执行

```
$ ./script.sh
```

上面例子中，`script.sh` 是脚本文件名。脚本通常使用`.sh`后缀名，不过这不是必需的

如果没有 Shebang 行，就只能手动将脚本传给解释器来执行

```
$ /bin/sh ./script.sh
# 或者
$ bash ./script.sh
```

## 执行权限和路径 

前面说过，只要指定了 Shebang 行的脚本，可以直接执行。这有一个前提条件，就是脚本需要有执行权限。可以使用下面的命令，赋予脚本执行权限。

```bash
# 给所有用户执行权限
$ chmod +x script.sh

# 给所有用户读权限和执行权限
$ chmod +rx script.sh
# 或者
$ chmod 755 script.sh

# 只给脚本拥有者读权限和执行权限
$ chmod u+rx script.sh
```

脚本的权限通常设为`755`
- 拥有者有所有权限，其他人有读和执行权限

或者 `700`
- 只有拥有者可以执行

除了执行权限，脚本调用时，一般需要指定脚本的路径（比如`path/script.sh`）。如果将脚本放在环境变量`$PATH`指定的目录中，就不需要指定路径了。因为 Bash 会自动到这些目录中，寻找是否存在同名的可执行文件。

建议在主目录新建一个 `~/bin` 子目录，专门存放可执行脚本，然后把 `~/bin` 加入 `$PATH`

```
export PATH=$PATH:~/bin
```

上面命令改变环境变量 `$PATH`，将 `~/bin` 添加到 `$PATH` 的末尾

可以将这一行加到 `~/.bashrc` 文件里面，然后重新加载一次`.bashrc`，这个配置就可以生效了

```
$ source ~/.bashrc
```

以后不管在什么目录，直接输入脚本文件名，脚本就会执行

## env 命令

`env` 这个二进制文件总是在目录 `/usr/bin`

`#!/usr/bin/env NAME` 这个语法的意思是，让 Shell 查找 `$PATH` 环境变量里面第一个匹配的 `NAME`

如果你不知道某个命令的具体路径，或者希望兼容其他用户的机器，这样的写法就很有用

`/usr/bin/env bash`的意思就是，返回 `bash` 可执行文件的位置，前提是 `bash` 的路径是在 `$PATH` 里面

其他脚本文件也可以使用这个命令。比如 Node.js 脚本的 Shebang 行，可以写成下面这样

```
#!/usr/bin/env node
```

`env` 命令的参数如下

*   `-i`, `--ignore-environment`：不带环境变量启动
*   `-u`, `--unset=NAME`：从环境变量中删除一个变量
*   `--help`：显示帮助
*   `--version`：输出版本信息

下面是一个例子，新建一个不带任何环境变量的 Shell

```
$ env -i /bin/sh
```

## 注释

Bash 脚本中，`#` 表示注释

建议在脚本开头，使用注释说明当前脚本的作用，这样有利于日后的维护

## 脚本参数

调用脚本的时候，脚本文件名后面可以带有参数

```
$ script.sh word1 word2 word3    # script.sh 是一个脚本文件，word1、word2 和 word3 是三个参数
```

脚本文件内部，可以使用特殊变量，引用这些参数

*   `$0`：脚本文件名，即 `script.sh`
*   `$1`~`$9`：对应脚本的第一个参数到第九个参数
*   `$#`：参数的总数
*   `$@`：全部的参数，参数之间使用空格分隔
*   `$*`：全部的参数，参数之间使用变量 `$IFS` 值的第一个字符分隔，默认为空格，但是可以自定义

如果脚本的参数多于 9 个，那么第 10 个参数可以用`${10}`的形式引用，以此类推

注意，如果命令是 `command -o foo bar`，那么 `-o` 是 `$1` ，`foo` 是 `$2`，`bar` 是 `$3`

下面是一个脚本内部读取命令行参数的例子

```
#!/bin/bash

for i in "$@"; do
  echo $i
done
```

## shift 命令

`shift` 命令可以改变脚本参数，每次执行都会移除脚本当前的第一个参数（`$1`），使得后面的参数向前一位，即`$2`变成`$1`、`$3`变成`$2`、`$4`变成`$3`，以此类推

`while` 循环结合 `shift` 命令，也可以读取每一个参数

```bash
#!/bin/bash

echo "一共输入了 $# 个参数"

while [ "$1" != "" ]; do
  echo "剩下 $# 个参数"
  echo "参数：$1"
  shift
done
```

上面例子中，`shift` 命令每次移除当前第一个参数，从而通过 `while` 循环遍历所有参数

`shift` 命令可以接受一个整数作为参数，指定所要移除的参数个数，默认为 `1`

```
shift 3
```

上面的命令移除前三个参数，原来的 `$4` 变成 `$1`

## getopts 命令

`getopts` 命令用在脚本内部，可以解析复杂的脚本命令行参数，通常与 `while` 循环一起使用，取出脚本所有的带有前置连词线（`-`）的参数

```
getopts optstring name
```

它带有两个参数
- 第一个参数 `optstring` 是字符串，给出脚本所有的连词线参数
  - 比如，某个脚本可以有三个配置项参数 `-l`、`-h`、`-a`，其中只有 `-a` 可以带有参数值，而`-l`和`-h`是开关参数，那么 `getopts` 的第一个参数写成 `lha:`，顺序不重要
  - 注意，`a` 后面有一个冒号，表示该参数带有参数值
  - `getopts` 规定带有参数值的配置项参数，后面必须带有一个冒号（`:`）
- 第二个参数 `name` 是一个变量名，用来保存当前取到的配置项参数，即 `l` `h` `a`

`OPTARG` 是 `getopts` 命令用到的一个全局变量，保存解析出来的带冒号选项后面的参数值

`OPTIND` 是 `getopts` 命令用到的一个全局变量，保存下一个待解析的参数 index

下面是一个例子

```BASH
while getopts 'lha:' OPTION; do
  case "$OPTION" in
    l)
      echo "linuxconfig"
      ;;

    h)
      echo "h stands for h"
      ;;

    a)
      avalue="$OPTARG"
      echo "The value provided is $OPTARG"
      ;;
    ?)
      echo "script usage: $(basename $0) [-l] [-h] [-a somevalue]" >&2
      exit 1
      ;;
  esac
done
shift "$(($OPTIND - 1))"
```

上面例子中

1. `while` 循环不断执行 `getopts 'lha:' OPTION` 命令，每次执行读取一个参数
  - 比如 `-a foo`环境变量，`foo` 是 `a` 的参数值
    - 当前处理的参数 `a` 保存在变量 `OPTION`
    - `$OPTARG` 保存的就是参数值
2. 然后进入 `case`

注意，只要遇到不带连词线的参数，`getopts` 就会执行失败，从而退出 `while` 循环

- 比如，`getopts` 可以解析 `command -l foo`，但不可以解析 `command foo -l`
- 多个连词线参数写在一起的形式，比如 `command -lh`，`getopts` 也可以正确处理

变量 `$OPTIND` 在 `getopts` 开始执行前是 `1`，然后每次执行就会加 `1`

等到退出 `while` 循环，就意味着连词线参数全部处理完毕

这时，`$OPTIND - 1` 就是已经处理的连词线参数个数，使用 `shift` 命令将这些参数移除，保证后面的代码可以用 `$1`、`$2` 等处理命令的主参数

## 配置项参数终止符 --

`-` 和 `--` 开头的参数，会被 Bash 当作配置项解释。但是，有时它们不是配置项，而是实体参数的一部分，比如文件名叫做 `-f` 或 `--file`

```
$ cat -f
$ cat --file
```

上面命令的原意是输出文件 `-f` 和 `--file` 的内容，但是会被 Bash 当作配置项解释

这时就可以使用配置项参数终止符`--`，它的作用是告诉 Bash，在它后面的参数开头的`-`和`--`不是配置项，只能当作实体参数解释

```
$ cat -- -f
$ cat -- --file
```

如果要确保某个变量不会被当作配置项解释，就要在它前面放上参数终止符 `--`

```
$ ls -- $myPath

```


另一个例子，如果想在文件里面搜索 `--hello`，这时也要使用参数终止符 `--`

```
$ grep -- "--hello" example.txt
```

## exit 命令

`exit` 命令用于终止当前脚本的执行，并向 Shell 返回一个退出值

```
$ exit
```

上面命令中止当前脚本，将最后一条命令的退出状态，作为整个脚本的退出状态

`exit` 命令后面可以跟参数，该参数就是退出状态

```
# 退出值为 0（成功）
$ exit 0

# 退出值为 1（失败）
$ exit 1
```

退出时，脚本会返回一个退出值
- `0` 表示正常
- `1` 表示发生错误
- `2` 表示用法不对
- `126` 表示不是可执行脚本
- `127` 表示命令没有发现
- 如果脚本被信号 `N` 终止，则退出值为`128 + N`。

简单来说，只要退出值非 0，就认为执行出错

下面是一个例子

```
if [ $(id -u) != "0" ]; then
  echo "根用户才能执行当前脚本"
  exit 1
fi
```

`id -u` 命令返回用户的 ID，一旦用户的 ID 不等于 `0`（根用户的 ID），脚本就会退出，退出码为 `1`

`exit` 与 `return` 命令的差别是
- `return` 命令是函数的退出，并返回一个值给调用者，脚本依然执行
- `exit` 是整个脚本的退出，如果在函数之中调用`exit`，则退出函数，并终止脚本执行

## 命令执行结果

命令执行结束后，会有一个返回值

环境变量 `$?` 可以读取前一个命令的返回值

利用这一点，可以在脚本中对命令执行结果进行判断

```bash
cd /path/to/somewhere
if [ "$?" = "0" ]; then
  rm *
else
  echo "Could not change directory! Aborting." 1>&2
  exit 1
fi
```

上面例子中，`cd /path/to/somewhere` 这个命令如果执行成功就删除该目录里面的文件，否则退出脚本，整个脚本的返回值变为 `1`，表示执行失败

`if` 可以直接判断命令的执行结果，上面的脚本等价下面的样子

```bash
if cd /path/to/somewhere; then
  rm *
else
  echo "Could not change directory! Aborting." 1>&2
  exit 1
fi
```

更简洁的写法是利用两个逻辑运算符 `&&` 和 `||`

```bash
# 第一步执行成功，才会执行第二步
cd /path/to/somewhere && rm *

# 第一步执行失败，才会执行第二步
cd /path/to/somewhere || exit 1
```

## source 命令

`source` 命令用于执行一个脚本，通常用于重新加载一个配置文件

```bash
$ source .bashrc
```

`source` 命令最大的特点是在当前 Shell 执行脚本，不像直接执行脚本时，会新建一个子 Shell

所以，`source` 命令执行脚本时，不需要 `export` 变量

```bash
#!/bin/bash
# test.sh
echo $foo
```

```bash
# 当前 Shell 新建一个变量 foo
$ foo=1

# 输出 1
$ source test.sh
1

# 输出空
$ bash test.sh
```

`source` 命令的另一个用途，是在脚本内部加载外部库

```
#!/bin/bash

source ./lib.sh

function_from_lib

```

上面脚本在内部使用 `source` 命令加载了一个外部库，然后就可以在脚本里面，使用这个外部库定义的函数

`source` 有一个简写形式，可以使用一个点（`.`）来表示

```bash
$ . .bashrc
```

## 别名，alias 命令

`alias` 命令用来为一个命令指定别名，这样更便于记忆。下面是 `alias` 的格式

```bash
alias NAME=DEFINITION
```

上面命令中，`NAME` 是别名的名称，`DEFINITION` 是别名对应的原始命令

1. 一个常见的例子是为 `grep` 命令起一个 `search` 的别名

```bash
alias search=grep
```

2. 下面是通过别名定义一个 `today` 的命令

```bash
$ alias today='date +"%A, %B %-d, %Y"'
$ today
星期一, 一月 6, 2020
```

3. 有时为了防止误删除文件，可以指定 `rm` 命令的别名

```bash
$ alias rm='rm -i'
```

4. `alias` 定义的别名也可以接受参数，参数会直接传入原始命令

```bash
$ alias echo='echo It says: '
$ echo hello world
It says: hello world
# 别名定义了 echo 命令的前两个参数，等同于修改了 echo 命令的默认行为
```

一般来说，都会把常用的别名写在 `~/.bashrc` 的末尾

另外，只能为命令定义别名，为其他部分（比如很长的路径）定义别名是无效的

直接调用 `alias` 命令，可以显示所有别名


`unalias` 命令可以解除别名


# 判断表达式 condition

`condition` 是一个命令

这个命令可以是 `test` 命令，也可以是其他命令

命令的返回值为 `0` 表示判断成立

常用的判断表达式有下面这些

## test 命令

`if` 结构的判断条件，一般使用 `test` 命令，有三种形式

```
# 写法一
test expression

# 写法二
[ expression ]

# 写法三
[[ expression ]]
```

上面三种形式是等价的，但是**第三种形式还支持正则判断**，前两种不支持

上面的 `expression` 是一个表达式

- 这个表达式为真，`test` 命令执行成功（返回值为`0`）；
- 表达式为伪，`test` 命令执行失败（返回值为`1`）

注意，第二种和第三种写法，`[` 和 `]` 与内部的表达式之间必须有空格

实际上，`[` 这个字符是 `test` 命令的一种简写形式，可以看作是一个独立的命令，这解释了为什么它后面必须有空格

下面把 `test` 命令的三种形式，用在 `if` 结构中，判断一个文件是否存在

```
# 写法一
if test -e /tmp/foo.txt ; then
  echo "Found foo.txt"
fi

# 写法二
if [ -e /tmp/foo.txt ] ; then
  echo "Found foo.txt"
fi

# 写法三
if [[ -e /tmp/foo.txt ]] ; then
  echo "Found foo.txt"
fi
```

## 文件判断

以下表达式用来判断文件状态。

*   `[ -a file ]`：如果 file 存在，则为`true`
*   `[ -b file ]`：如果 file 存在并且是一个块（设备）文件，则为`true`
*   `[ -c file ]`：如果 file 存在并且是一个字符（设备）文件，则为`true`
*   `[ -d file ]`：如果 file 存在并且是一个目录，则为`true`
*   `[ -e file ]`：如果 file 存在，则为`true`
*   `[ -f file ]`：如果 file 存在并且是一个普通文件，则为`true`
*   `[ -g file ]`：如果 file 存在并且设置了组 ID，则为`true`
*   `[ -G file ]`：如果 file 存在并且属于有效的组 ID，则为`true`
*   `[ -h file ]`：如果 file 存在并且是符号链接，则为`true`
*   `[ -k file ]`：如果 file 存在并且设置了它的“sticky bit”，则为`true`
*   `[ -L file ]`：如果 file 存在并且是一个符号链接，则为`true`
*   `[ -N file ]`：如果 file 存在并且自上次读取后已被修改，则为`true`
*   `[ -O file ]`：如果 file 存在并且属于有效的用户 ID，则为`true`
*   `[ -p file ]`：如果 file 存在并且是一个命名管道，则为`true`
*   `[ -r file ]`：如果 file 存在并且可读（当前用户有可读权限），则为`true`
*   `[ -s file ]`：如果 file 存在且其长度大于零，则为`true`
*   `[ -S file ]`：如果 file 存在且是一个网络 socket，则为`true`
*   `[ -t fd ]`：如果 fd 是一个文件描述符，并且重定向到终端，则为`true`。 这可用来判断是否重定向了标准输入／输出／错误
*   `[ -u file ]`：如果 file 存在并且设置了 setuid 位，则为`true`
*   `[ -w file ]`：如果 file 存在并且可写（当前用户拥有可写权限），则为`true`
*   `[ -x file ]`：如果 file 存在并且可执行（有效用户有执行／搜索权限），则为`true`
*   `[ FILE1 -nt FILE2 ]`：如果 FILE1 比 FILE2 的更新时间更近，或者 FILE1 存在而 FILE2 不存在，则为`true`
*   `[ FILE1 -ot FILE2 ]`：如果 FILE1 比 FILE2 的更新时间更旧，或者 FILE2 存在而 FILE1 不存在，则为`true`
*   `[ FILE1 -ef FILE2 ]`：如果 FILE1 和 FILE2 引用相同的设备和 inode 编号，则为`true`

下面是一个示例

```bash
#!/bin/bash

FILE=~/.bashrc

if [ -e "$FILE" ]; then
  if [ -f "$FILE" ]; then
    echo "$FILE is a regular file."
  fi
  if [ -d "$FILE" ]; then
    echo "$FILE is a directory."
  fi
  if [ -r "$FILE" ]; then
    echo "$FILE is readable."
  fi
  if [ -w "$FILE" ]; then
    echo "$FILE is writable."
  fi
  if [ -x "$FILE" ]; then
    echo "$FILE is executable/searchable."
  fi
else
  echo "$FILE does not exist"
  exit 1
fi

```

上面代码中，`$FILE` 要放在双引号之中，这样可以防止变量 `$FILE` 为空，从而出错

- `$FILE` 如果为空，这时 `[ -e $FILE ]` 就变成 `[ -e ]` ，这会被判断为真
- `$FILE` 放在双引号之中，`[ -e "$FILE" ]`就变成`[ -e "" ]`，这会被判断为伪

## 字符串判断

以下表达式用来判断字符串

*   `[ string ]`：如果`string`不为空（长度大于0），则判断为真
*   `[ -n string ]`：如果字符串`string`的长度大于零，则判断为真
*   `[ -z string ]`：如果字符串`string`的长度为零，则判断为真
*   `[ string1 = string2 ]`：如果`string1`和`string2`相同，则判断为真
*   `[ string1 == string2 ]` 等同于`[ string1 = string2 ]`
*   `[ string1 != string2 ]`：如果`string1`和`string2`不相同，则判断为真
*   `[ string1 '>' string2 ]`：如果按照字典顺序`string1`排列在`string2`之后，则判断为真
*   `[ string1 '<' string2 ]`：如果按照字典顺序`string1`排列在`string2`之前，则判断为真

注意，`test` 命令内部的 `>` 和 `<` ，必须用引号引起来（或者是用反斜杠转义）

否则，它们会被 shell 解释为重定向操作符

下面是一个示例

```bash
#!/bin/bash

ANSWER=maybe

if [ -z "$ANSWER" ]; then
  echo "There is no answer." >&2
  exit 1
fi
if [ "$ANSWER" = "yes" ]; then
  echo "The answer is YES."
elif [ "$ANSWER" = "no" ]; then
  echo "The answer is NO."
elif [ "$ANSWER" = "maybe" ]; then
  echo "The answer is MAYBE."
else
  echo "The answer is UNKNOWN."
fi
```

上面代码中

1. 首先确定 `$ANSWER` 字符串是否为空，如果为空，就终止脚本，并把退出状态设为 `1`
   - 注意，这里的 `echo` 命令把错误信息 `There is no answer.` 重定向到标准错误，这是处理错误信息的常用方法

2. `$ANSWER` 字符串不为空，就判断它的值是否等于 `yes` 、`no` 或者 `maybe`
   - 注意，字符串判断时，变量要放在双引号之中

## 整数判断

下面的表达式用于判断整数

*   `[ integer1 -eq integer2 ]`：如果`integer1`等于`integer2`，则为`true`
*   `[ integer1 -ne integer2 ]`：如果`integer1`不等于`integer2`，则为`true`
*   `[ integer1 -le integer2 ]`：如果`integer1`小于或等于`integer2`，则为`true`
*   `[ integer1 -lt integer2 ]`：如果`integer1`小于`integer2`，则为`true`
*   `[ integer1 -ge integer2 ]`：如果`integer1`大于或等于`integer2`，则为`true`
*   `[ integer1 -gt integer2 ]`：如果`integer1`大于`integer2`，则为`true`

下面是一个用法的例子

```bash
#!/bin/bash

INT=-5

if [ -z "$INT" ]; then
  echo "INT is empty." >&2
  exit 1
fi
if [ $INT -eq 0 ]; then
  echo "INT is zero."
else
  if [ $INT -lt 0 ]; then
    echo "INT is negative."
  else
    echo "INT is positive."
  fi
  if [ $((INT % 2)) -eq 0 ]; then
    echo "INT is even."
  else
    echo "INT is odd."
  fi
fi
```

先判断变量 `$INT` 是否为空，然后判断是否为 `0` ，接着判断正负，最后通过求余数判断奇偶

## 正则判断

`[[ expression ]]` 这种判断形式，支持正则表达式

```
[[ string1 =~ regex ]]
```

上面的语法中，`regex` 是一个正则表示式，`=~` 是正则比较运算符

下面是一个例子

```bash
#!/bin/bash

INT=-5

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
  echo "INT is an integer."
  exit 0
else
  echo "INT is not an integer." >&2
  exit 1
fi
```

上面代码中，判断变量 `INT` 的字符串形式，是否满足 `^-?[0-9]+$` 的正则模式

## 算术判断

Bash 还提供了 `((...))` 作为算术条件，进行算术运算的判断

```
if ((3 > 2)); then
  echo "true"
fi
```

上面代码执行后，会打印出 `true`

如果算术计算的结果是非零值，则表示判断成立，这一点跟命令的返回值正好相反，需要小心

下面是用算术条件改写的数值判断脚本

```bash
#!/bin/bash

INT=-5

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
  if ((INT == 0)); then
    echo "INT is zero."
  else
    if ((INT < 0)); then
      echo "INT is negative."
    else
      echo "INT is positive."
    fi
    if (( ((INT % 2)) == 0)); then
      echo "INT is even."
    else
      echo "INT is odd."
    fi
  fi
else
  echo "INT is not an integer." >&2
  exit 1
fi

```

只要是算术表达式，都能用于 `((...))` 语法，详见《Bash 的算术运算》一章

## test 判断的逻辑运算

通过逻辑运算，可以把多个 `test` 判断表达式结合起来，创造更复杂的判断

三种逻辑运算 `AND`，`OR` 和 `NOT`，都有自己的专用符号

*   `AND` 运算：符号 `&&`，也可使用参数 `-a`
*   `OR` 运算：符号 `||`，也可使用参数 `-o`
*   `NOT` 运算：符号 `!`

下面是一个 `AND` 的例子，判断整数是否在某个范围之内

```bash
#!/bin/bash

MIN_VAL=1
MAX_VAL=100

INT=50

if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
  if [[ $INT -ge $MIN_VAL && $INT -le $MAX_VAL ]]; then
    echo "$INT is within $MIN_VAL to $MAX_VAL."
  else
    echo "$INT is out of range."
  fi
else
  echo "INT is not an integer." >&2
  exit 1
fi

```

使用否定操作符`!`时，最好用圆括号确定转义的范围

```bash
if [ ! \( $INT -ge $MIN_VAL -a $INT -le $MAX_VAL \) ]; then
    echo "$INT is outside $MIN_VAL to $MAX_VAL."
else
    echo "$INT is in range."
fi
```

上面例子中，`test` 命令内部使用的圆括号，必须使用引号或者转义，否则会被 Bash 解释

## 普通命令的逻辑运算

可以使用 Bash 的命令控制操作符 `&&` 和 `||` ，进行多个命令的逻辑运算

- 对于`&&`操作符，先执行`command1`，只有`command1`执行成功后， 才会执行`command2`
- 对于`||`操作符，先执行`command1`，只有`command1`执行失败后， 才会执行`command2`


下面是一个示例

```bash
#! /bin/bash

filename=$1
word1=$2
word2=$3

if grep $word1 $filename && grep $word2 $filename
then
  echo "$word1 and $word2 are both in $filename."
fi
```


下面的示例演示如何将一个 `&&` 判断表达式，改写成对应的 `if` 结构

```bash
[[ -d "$dir_name" ]] && cd "$dir_name" && rm *

# 等同于

if [[ ! -d "$dir_name" ]]; then
  echo "No such directory: '$dir_name'" >&2
  exit 1
fi
if ! cd "$dir_name"; then
  echo "Cannot cd to '$dir_name'" >&2
  exit 1
fi
if ! rm *; then
  echo "File deletion failed. Check results" >&2
  exit 1
fi
```


# 分支

Bash 脚本的条件判断语法

## if 结构

`if` 是最常用的条件判断结构，它的语法如下

```bash
if condition; then
  commands
elif condition; then
  commands...
else
  commands
fi
```

这个命令分成三个部分：`if`、`elif` 和 `else`

其中，后两个部分是可选的

```
if test $USER = "foo"; then
  echo "Hello foo."
else
  echo "You are not foo."
fi
```

上面的例子中，判断条件是环境变量 `$USER` 是否等于 `foo`，如果等于就输出 `Hello foo.`，否则输出其他内容

`if` 和 `then` 写在同一行时，需要分号分隔，分号是 Bash 的命令分隔符

它们也可以写成两行，这时不需要分号

```
# true 和 false 是两个特殊命令

if true
then
  echo 'hello world'
fi
```

除了多行的写法，`if` 结构也可以写成单行

```
$ if true; then echo 'hello world'; fi
hello world
```

注意
- `if` 后面也可以是一条命令，该条命令执行成功，返回值 `0`，就意味着判断条件成立
  - `if` 后面可以跟任意数量的命令
  - 这时，所有命令都会执行，但是判断真伪只看最后一个命令

```
$ if false; true; then echo 'hello world'; fi
hello world
```

上面例子中，`if` 后面有两条命令（`false;true;`），第二条命令（`true`）决定了`then` 的部分是否会执行

## case 结构

```bash
case expression in
  pattern )
    commands ;;
  pattern )
    commands ;;
  ...
esac
```

上面代码中，`expression` 是一个表达式，`pattern` 是表达式的值或者一个模式，可以有多条，用来匹配多个值，每条以两个分号（`;`）结尾

```bash
#!/bin/bash

echo -n "输入一个1到3之间的数字（包含两端）> "
read character
case $character in
  1 ) echo input is 1
    ;;
  2 ) echo input is 2
    ;;
  3 ) echo input is 3
    ;;
  * ) echo 输入不符合要求
esac

```

上面例子中，最后一条匹配语句的模式是 `*`，这个通配符可以匹配其他字符和没有输入字符的情况，类似 `if` 的 `else` 部分


`case` 的匹配模式可以使用各种通配符，下面是一些例子

*   `a)`：匹配`a`
*   `a|b)`：匹配`a`或`b`
*   `[[:alpha:]])`：匹配单个字母
*   `???)`：匹配3个字符的单词
*   `*.txt)`：匹配`.txt`结尾
*   `*)`：匹配任意输入，通过作为`case`结构的最后一个模式


- Bash 4.0 之前，`case` 结构只能匹配一个条件，然后就会退出 `case` 结构
- Bash 4.0 之后，允许匹配多个条件，这时可以用 `;;&` 只终止每个条件块

```bash
#!/bin/bash
# test.sh

read -n 1 -p "Type a character > "
echo
case $REPLY in
  [[:upper:]])    echo "'$REPLY' is upper case." ;;&
  [[:lower:]])    echo "'$REPLY' is lower case." ;;&
  [[:alpha:]])    echo "'$REPLY' is alphabetic." ;;&
  [[:digit:]])    echo "'$REPLY' is a digit." ;;&
  [[:graph:]])    echo "'$REPLY' is a visible character." ;;&
  [[:punct:]])    echo "'$REPLY' is a punctuation symbol." ;;&
  [[:space:]])    echo "'$REPLY' is a whitespace character." ;;&
  [[:xdigit:]])   echo "'$REPLY' is a hexadecimal digit." ;;&
esac
```

执行上面的脚本，会得到下面的结果

```
$ test.sh
Type a character > a
'a' is lower case.
'a' is alphabetic.
'a' is a visible character.
'a' is a hexadecimal digit.
```

可以看到条件语句结尾添加了 `;;&` 以后，在匹配一个条件之后，并没有退出 `case` 结构，而是继续判断下一个条件


# 循环

Bash 提供三种循环语法 `for` `while` `until`

## while 循环

循环条件 `condition` 跟 `if` 结构的判断条件写法一致

`while` 循环有一个判断条件，只要符合条件，就不断循环执行指定的语句

```bash
while condition; do
  commands
done
```

关键字 `do` 可以跟 `while` 不在同一行，这时两者之间不需要使用分号分隔

```bash
while condition
do
  commands
done
```

`while` 循环写成一行，也是可以的

```
$ while condition; do commands; done
```

## until 循环

`until` 循环与 `while` 循环恰好相反，只要判断条件失败，就不断循环执行指定的语句

```bash
until condition; do
  commands
done
```

`until` 循环都可以转为 `while` 循环，只要把条件设为否定即可

一般来说，`until` 用得比较少，完全可以统一都使用 `while`

## for...in 循环

`for...in` 循环用于遍历列表的每一项

```bash
for variable in list; do
  commands
done
```

```bash
for variable in list
do
  commands
done
```

上面语法中，`for` 循环会依次从 `list` 列表中取出一项，作为变量 `variable`，然后在循环体中进行处理

下面是一个例子

```bash
#!/bin/bash

for i in word1 word2 word3; do
  echo $i
done
```

- 列表可以由通配符产生

```bash
for i in *.png; do
  ls -l $i
done
```

上面例子中，`*.png` 会替换成当前目录中所有 PNG 图片文件

- 列表也可以通过子命令产生

```bash
#!/bin/bash

count=0
for i in $(cat ~/.bash_profile); do
  count=$((count + 1))
  echo "Word $count ($i) contains $(echo -n $i | wc -c) characters"
done
```

- `in list` 的部分可以省略，这时 `list` 默认等于脚本的所有参数 `$@`

但是，为了可读性，最好还是不要省略，参考下面的例子

```bash
for filename; do
  echo "$filename"
done

# 等同于

for filename in "$@" ; do
  echo "$filename"
done

```

在函数体中也是一样的

`for...in` 循环省略 `in list` 的部分，则 `list` 默认等于函数的所有参数

## for 循环

`for` 循环还支持 C 语言的循环语法

```bash
for (( expression1; expression2; expression3 )); do
  commands
done
```

- 注意，循环条件放在双重圆括号之中
- 另外，圆括号之中使用变量，不必加上美元符号 `$`

它等同于下面的 `while` 循环

```bash
(( expression1 ))
while (( expression2 )); do
  commands
  (( expression3 ))
done
```

下面是一个例子

```bash
for (( i=0; i<5; i=i+1 )); do
  echo $i
done
```

## break，continue

Bash 提供了两个内部命令 `break` 和 `continue`，用来在循环内部跳出循环

```
#!/bin/bash

for number in 1 2 3 4 5 6
do
  echo "number is $number"
  if [ "$number" = "3" ]; then
    break
  fi
done
```

## select 结构

`select` 结构主要用来生成简单的菜单

它的语法与 `for...in` 循环基本一致

```bash
select name
[in list]
do
  commands
done
```

Bash 会对 `select` 依次进行下面的处理

1.  `select` 生成一个菜单，内容是列表 `list` 的每一项，并且每一项前面还有一个数字编号
2.  Bash 提示用户选择一项，输入它的编号
3.  用户输入以后，Bash 会将该项的内容存在变量 `name`，该项的编号存入环境变量 `REPLY`
    - 如果用户没有输入，就按回车键，Bash 会重新输出菜单，让用户选择
4.  执行命令体 `commands`
5.  执行结束后，回到第一步，重复这个过程

直到用户按下`Ctrl + c`，退出执行

下面是一个例子

```bash
#!/bin/bash
# select.sh

select brand in Samsung Sony iphone symphony Walton
do
  echo "You have chosen $brand"
done
```

执行上面的脚本，Bash 会输出一个品牌的列表，让用户选择

```
$ ./select.sh
1) Samsung
2) Sony
3) iphone
4) symphony
5) Walton
#? 1
You have chosen Samsung
```

`select` 可以与 `case` 结合，针对不同项，执行不同的命令

```bash
#!/bin/bash

echo "Which Operating System do you like?"

select os in Ubuntu LinuxMint Windows8 Windows10 WindowsXP
do
  case $os in
    "Ubuntu"|"LinuxMint")
      echo "I also use $os."
    ;;
    "Windows8" | "Windows10" | "WindowsXP")
      echo "Why don't you try Linux?"
    ;;
    *)
      echo "Invalid entry."
      break
    ;;
  esac
done
```


# Bash 函数

## 简介

函数（function）是可以重复使用的代码片段，有利于代码的复用

它与别名（alias）的区别是

- 别名只适合封装简单的单个命令
- 函数则可以封装复杂的多行命令



它与脚本的一个重大区别
- 函数总是在当前 Shell 执行
- Bash 会新建一个子 Shell 执行脚本

如果函数与脚本同名，函数会优先执行。但是，函数的优先级不如别名，即如果函数与别名同名，那么别名优先执行。

Bash 函数定义的语法有两种

```bash
# 第一种
fn() {
  # codes
}

# 第二种
function fn() {
  # codes
}
```

上面代码中，`fn` 是自定义的函数名，函数代码就写在大括号之中

下面是一个简单函数的例子

```bash
hello() {
  echo "Hello $1"
}
```

上面代码中，函数体里面的 `$1` 表示函数调用时的第一个参数

调用时，就直接写函数名，参数跟在函数名后面

```bash
$ hello world
Hello world
```

删除一个函数，可以使用 `unset` 命令

```bash
unset -f functionName
```

查看当前 Shell 已经定义的所有函数，可以使用 `declare` 命令

```bash
$ declare -f
```

上面的 `declare` 命令不仅会输出函数名，还会输出所有定义

输出顺序是按照函数名的字母表顺序

由于会输出很多内容，最好通过管道命令配合 `more` 或 `less` 使用

`declare -f functionName` 查看单个函数的定义

`declare -F` 可以输出所有已经定义的函数名，不含函数体

## 参数变量

函数体内可以使用参数变量，获取函数参数。函数的参数变量，与脚本参数变量是一致的。

*   `$1`~`$9`：函数的第一个到第9个的参数
*   `$0`：函数所在的脚本名
*   `$#`：函数的参数总数
*   `$@`：函数的全部参数，参数之间使用空格分隔
*   `$*`：函数的全部参数，参数之间使用变量 `$IFS` 值的第一个字符分隔，默认为空格，但是可以自定义

如果函数的参数多于9个，那么第10个参数可以用 `${10}` 的形式引用，以此类推

下面是一个示例脚本 `test.sh`

```bash
#!/bin/bash
# test.sh

function alice {
  echo "alice: $@"
  echo "$0: $1 $2 $3 $4"
  echo "$# arguments"

}

alice in wonderland
```

运行该脚本，结果如下

```bash
$ bash test.sh
alice: in wonderland
test.sh: in wonderland
2 arguments
```

上面例子中，由于函数`alice`只有第一个和第二个参数，所以第三个和第四个参数为空。

下面是一个日志函数的例子

```
function log_msg {
  echo "`date '+ %F %T'`: $@"
}
```

使用方法如下

```bash
$ log_msg "This is sample log message"
2018-08-16 19:56:34: This is sample log message
```

## return 命令

`return` 命令用于从函数返回一个值

```bash
function func_return_value {
  return 10
}
```

如果命令行直接执行函数，下一个命令可以用 `$?` 拿到返回值

```bash
$ func_return_value
$ echo "Value returned by function is: $?"
Value returned by function is: 10
```

`return` 后面不跟参数，只用于返回也是可以的

```bash
function name {
  commands
  return
}
```

## 全局变量和局部变量，local 命令

Bash 函数体内直接声明的变量，属于全局变量，整个脚本都可以读取

这一点需要特别小心

```bash
# 脚本 test.sh

fn () {
  foo=1
  echo "fn: foo = $foo"
}

fn
echo "global: foo = $foo"

```

上面脚本的运行结果如下

```
$ bash test.sh
fn: foo = 1
global: foo = 1
```

函数里面可以用 `local` 命令声明局部变量

```bash
#! /bin/bash
# 脚本 test.sh
fn () {
  local foo
  foo=1
  echo "fn: foo = $foo"
}

fn
echo "global: foo = $foo"
```

上面脚本的运行结果如下

```bash
$ bash test.sh
fn: foo = 1
global: foo =
```


# 脚本除错

如何对 Shell 脚本除错

运行语句前打印出即将运行的语句，方便排错

- `set -x`
- `bash -x`

## 常见错误

编写 Shell 脚本的时候，一定要考虑到命令失败的情况，否则很容易出错

例子如下

```basg
#!/bin/bash

dir_name=/path/not/exist

cd $dir_name
rm *
```

1. 如果目录 `$dir_name` 不存在，`cd $dir_name` 命令就会执行失败
2. 这时不会改变当前目录，脚本会继续执行下去，导致 `rm *` 命令删光当前目录的文件

如果改成下面的样子，也会有问题

```
cd $dir_name && rm *
```

- 如果变量 `$dir_name` 为空，`cd` 就会进入用户主目录，从而删光用户主目录的文件

下面的写法才是正确的

```bash
[[ -d $dir_name ]] && cd $dir_name && rm *
```

- 先判断目录 `$dir_name` 是否存在，然后才执行其他操作

如果不放心删除什么文件，可以先打印出来看一下

```bash
[[ -d $dir_name ]] && cd $dir_name && echo rm *
```

- `echo rm *` 不会删除文件，只会打印出来要删除的文件

## 用于除错的环境变量

有一些环境变量常用于除错

#### LINENO

变量 `LINENO` 返回它在脚本里面的行号

```bash
#!/bin/bash

echo "This is line $LINENO"
```

执行上面的脚本 `test.sh`，`$LINENO` 会返回 `3`

#### FUNCNAME

变量 `FUNCNAME` 返回一个数组，内容是**当前的函数调用堆栈**

该数组的 0 号成员是当前调用的函数，1 号成员是调用当前函数的函数，以此类推

```bash
#!/bin/bash

function func1()
{
  echo "func1: FUNCNAME0 is ${FUNCNAME[0]}"
  echo "func1: FUNCNAME1 is ${FUNCNAME[1]}"
  echo "func1: FUNCNAME2 is ${FUNCNAME[2]}"
  func2
}

function func2()
{
  echo "func2: FUNCNAME0 is ${FUNCNAME[0]}"
  echo "func2: FUNCNAME1 is ${FUNCNAME[1]}"
  echo "func2: FUNCNAME2 is ${FUNCNAME[2]}"
}

func1
```

执行上面的脚本 `test.sh`，结果如下

```bash
$ ./test.sh
func1: FUNCNAME0 is func1
func1: FUNCNAME1 is main
func1: FUNCNAME2 is
func2: FUNCNAME0 is func2
func2: FUNCNAME1 is func1
func2: FUNCNAME2 is main
```

#### BASH_SOURCE

变量 `BASH_SOURCE` 返回一个数组，内容是**当前的脚本调用堆栈**

该数组的 0 号成员是当前执行的脚本，1 号成员是调用当前脚本的脚本，以此类推

跟变量 `FUNCNAME` 相似

测试脚本如下 `lib1.sh` `lib2.sh` `main.sh`

```bash
# lib1.sh
function func1()
{
  echo "func1: BASH_SOURCE0 is ${BASH_SOURCE[0]}"
  echo "func1: BASH_SOURCE1 is ${BASH_SOURCE[1]}"
  echo "func1: BASH_SOURCE2 is ${BASH_SOURCE[2]}"
  func2
}
```

```bash
# lib2.sh
function func2()
{
  echo "func2: BASH_SOURCE0 is ${BASH_SOURCE[0]}"
  echo "func2: BASH_SOURCE1 is ${BASH_SOURCE[1]}"
  echo "func2: BASH_SOURCE2 is ${BASH_SOURCE[2]}"
}
```

```bash
#!/bin/bash
# main.sh: 主脚本，调用上面两个子脚本

source lib1.sh
source lib2.sh

func1
```

执行主脚本 `main.sh`，会得到下面的结果

```bash
$ ./main.sh
func1: BASH_SOURCE0 is lib1.sh
func1: BASH_SOURCE1 is ./main.sh
func1: BASH_SOURCE2 is
func2: BASH_SOURCE0 is lib2.sh
func2: BASH_SOURCE1 is lib1.sh
func2: BASH_SOURCE2 is ./main.sh
```

#### BASH_LINENO

变量 `BASH_LINENO` 返回一个数组，内容是**每一轮调用对应的行号**

在 `${BASH_SOURCE[i+1]}` 脚本的第 `${BASH_LINENO[i]}` 行，调用了 `${FUNCNAME[i]}`

测试脚本如下 `lib1.sh` `lib2.sh` `main.sh`

```bash
# lib1.sh
function func1()
{
  echo "func1: FUNCNAME is ${FUNCNAME[0]}"
  echo "func1: BASH_SOURCE is ${BASH_SOURCE[1]}"
  echo "func1: BASH_LINENO is ${BASH_LINENO[0]}"

  func2
}
```

```bash
# lib2.sh
function func2()
{
  echo "func2: FUNCNAME is ${FUNCNAME[0]}"
  echo "func2: BASH_SOURCE is ${BASH_SOURCE[1]}"
  echo "func2: BASH_LINENO is ${BASH_LINENO[0]}"
}
```

```bash
#!/bin/bash
# main.sh: 主脚本，调用上面两个子脚本

source lib1.sh
source lib2.sh

func1
```

执行主脚本 `main.sh`，会得到下面的结果

```bash
$ ./main.sh
func1: FUNCNAME is func1
func1: BASH_SOURCE is main.sh
func1: BASH_LINENO is 7
func2: FUNCNAME is func2
func2: BASH_SOURCE is lib1.sh
func2: BASH_LINENO is 8
```

上面例子中
- 函数 `func1` 是在 `main.sh` 的第 7 行调用的
- 函数 `func2` 是在 `lib1.sh` 的第 8 行调用的


# set 命令，shopt 命令

`set` 命令是 Bash 脚本的重要环节，却常常被忽视，导致脚本的安全性和可维护性出问题

`set` 用来设置脚本运行时遇到特殊情况时的行为

本章介绍 `set` 的基本用法，帮助你写出更安全的 Bash 脚本

## 简介

我们知道，Bash 执行脚本时，会创建一个子 Shell

```bash
$ bash script.sh
```

上面代码中，`script.sh` 是在一个子 Shell 里面执行

这个子 Shell 就是脚本的执行环境，Bash 默认给定了这个环境的各种参数

`set` 命令用来修改子 Shell 环境的运行参数，即定制环境

一共有十几个参数可以定制，[官方手册](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)有完整清单，本章介绍其中最常用的几个

顺便提一下，如果命令行下不带任何参数，直接运行 `set`，会显示所有的环境变量和 Shell 函数

```bash
$ set
```

## set -u

执行脚本时，如果遇到不存在的变量，Bash 默认忽略它

```bash
# a 是不存在的变量

echo $a
```

这不是开发者想要的行为，遇到变量不存在，脚本应该报错，而不是一声不响地往下执行

`set -u` 就用来改变这种行为

- 脚本在头部加上它，遇到不存在的变量就会报错，并停止执行

```bash
#!/usr/bin/env bash
set -u

echo $a
echo bar
```

运行结果如下

```bash
$ bash script.sh
bash: script.sh:行4: a: 未绑定的变量
```

`-u` 还有另一种写法 `-o nounset`

```
set -o nounset
```

## set -x

`set -x` 用来在运行结果之前，先输出执行的那一行命令

```bash
#!/usr/bin/env bash
set -x

echo bar
```

执行上面的脚本，结果如下

```bash
$ bash script.sh
+ echo bar
bar
```

可以看到，执行 `echo bar` 之前，该命令会先打印出来，行首以 `+` 表示

输出的命令之前的 `+` 号，是由系统变量 `PS4` 决定，可以修改这个变量

这对于调试复杂的脚本是很有用的

`-x` 还有另一种写法 `-o xtrace`

```bash
set -o xtrace
```

脚本当中如果要关闭 `-x`，可以使用 `set +x`

```bash
#!/bin/bash

number=1

set -x
if [ $number = "1" ]; then
  echo "Number equals 1"
else
  echo "Number does not equal 1"
fi
set +x
```

上面的例子中，只对特定的代码段打开 `输出执行的那一行命令`

## 脚本的错误处理

如果脚本里面有运行失败的命令（返回值非 `0`），Bash 默认会继续执行后面的命令

```bash
#!/usr/bin/env bash

foo
echo bar
```

```bash
$ bash script.sh
script.sh:行3: foo: 未找到命令
bar
```

可以看到，Bash 只是显示有错误，并没有终止执行

这种行为很不利于脚本安全和除错

实际开发中，如果某个命令失败，往往需要脚本停止执行，防止错误累积

这时，一般采用下面的写法

```bash
command || exit 1
```

上面的写法表示只要 `command` 有非零返回值，脚本就会停止执行

如果停止执行之前需要完成多个操作，就要采用下面三种写法

```bash
# 写法一
command || { op1; op2; }

# 写法二
if ! command; then op1; op2; fi

# 写法三
command
if [ "$?" -ne 0 ]; then op1; op2; fi
```

如果两个命令有继承关系，只有第一个命令成功了，才能继续执行第二个命令，那么就要采用下面的写法

```bash
command1 && command2
```

## set -e

`set -e` 它使得脚本只要发生错误，就终止执行

`set -e` 根据返回值来判断，一个命令是否运行失败

```bash
#!/usr/bin/env bash
set -e

foo
echo bar
```

执行结果如下

```bash
$ bash script.sh
script.sh:行4: foo: 未找到命令
```

可以看到，第 4 行执行失败以后，脚本就终止执行了

有时我们要求命令即使执行失败，脚本也不会终止执行，可以暂时关闭 `-e`

脚本当中如果要关闭 `-e`，可以使用 `set +e`

```bash
set +e
command1
command2
set -e
```

上面代码中，`set +e` 表示关闭 `-e` 选项，`set -e` 表示重新打开 `-e` 选项

还有一种方法使得该命令即使执行失败，脚本也不会终止执行

```bash
#!/bin/bash
set -e

foo || true
echo bar
```

上面代码中，`true` 使得这一行语句总是会执行成功，后面的 `echo bar` 会执行

`-e` 还有另一种写法 `-o errexit`

## set -o pipefail

`set -e` 有一个例外情况，就是不适用于管道命令

所谓管道命令，就是多个子命令通过管道运算符（`|`）组合成为一个大的命令

Bash 会把最后一个子命令的返回值，作为整个命令的返回值

也就是说，只要最后一个子命令不失败，管道命令总是会执行成功

请看下面这个例子

```bash
#!/usr/bin/env bash
set -e

foo | echo a
echo bar
```

执行结果如下

```bash
$ bash script.sh
a
script.sh:行4: foo: 未找到命令
bar
```

上面代码中，`foo` 是一个不存在的命令，但是 `foo | echo a` 这个管道命令会执行成功，导致后面的 `echo bar` 会继续执行

`set -o pipefail` 用来解决这种情况，只要一个子命令失败，整个管道命令就失败，脚本就会终止执行

```bash
#!/usr/bin/env bash
set -eo pipefail

foo | echo a
echo bar
```

运行后，结果如下

```bash
$ bash script.sh
a
script.sh:行4: foo: 未找到命令
```

可以看到，`echo bar` 没有执行

## set -E ??

一旦设置了 `-e` 参数，会导致函数内的错误不会被 `trap` 命令捕获

`-E` 参数可以纠正这个行为，使得函数也能继承 `trap` 命令

```
#!/bin/bash
set -e

trap "echo ERR trap fired!" ERR

myfunc()
{
  # 'foo' 是一个不存在的命令
  foo
}

myfunc

```

上面示例中，`myfunc`函数内部调用了一个不存在的命令`foo`，导致执行这个函数会报错。

```
$ bash test.sh
test.sh:行9: foo：未找到命令

```

但是，由于设置了`set -e`，函数内部的报错并没有被`trap`命令捕获，需要加上`-E`参数才可以。

```
#!/bin/bash
set -Eeuo pipefail

trap "echo ERR trap fired!" ERR

myfunc()
{
  # 'foo' 是一个不存在的命令
  foo
}

myfunc

```

执行上面这个脚本，就可以看到`trap`命令生效了。

```
$ bash test.sh
test.sh:行9: foo：未找到命令
ERR trap fired!

```

## 其他参数

`set` 命令还有一些其他参数

*   `set -n`：等同于 `set -o noexec`，不运行命令，只检查语法是否正确
*   `set -f`：等同于 `set -o noglob`，表示不对通配符进行文件名扩展
*   `set -v`：等同于 `set -o verbose`，表示打印 Shell 接收到的每一行输入
*   `set -o noclobber`：防止使用重定向运算符 `>` 覆盖已经存在的文件

上面的 `-f` 和 `-v` 参数，可以分别使用 `set +f`、`set +v` 关闭

## set 参数的组合使用

上面重点介绍的 `set` 命令的几个参数，一般都放在一起使用

```bash
# 写法一
set -Eeuxo pipefail

# 写法二
set -Eeux
set -o pipefail
```

这两种写法建议放在所有 Bash 脚本的头部

另一种办法是在执行 Bash 脚本的时候，从命令行传入这些参数

```bash
$ bash -euxo pipefail script.sh
```

## shopt 命令

`shopt` 命令用来调整 Shell 的参数，跟 `set` 命令的作用很类似

之所以会有这两个类似命令的主要原因是

- `set` 是从 Ksh 继承的，属于 POSIX 规范的一部分
- `shopt` 是 Bash 特有的

直接输入 `shopt` 可以查看所有参数，以及它们各自打开和关闭的状态

```bash
$ shopt
```

`shopt` 命令后面跟着参数名，可以查询该参数是否打开

```bash
$ shopt globstar
globstar  off          # 表示 globstar 参数默认是关闭的
```

1. -s

`-s` 用来打开某个参数

```bash
$ shopt -s optionName
```

2. -u

`-u` 用来关闭某个参数 

```bash
$ shopt -u optionName
```

举例来说，`histappend` 这个参数表示退出当前 Shell 时，将操作历史追加到历史文件中

这个参数默认是打开的，如果使用下面的命令将其关闭，那么当前 Shell 的操作历史将替换掉整个历史文件

```bash
$ shopt -u histappend
```

3. -q

`-q` 的作用也是查询某个参数是否打开，用 `$?` 查询结果

如果状态为 `0`，表示该参数打开；如果为 `1`，表示该参数关闭

```bash
$ shopt -q globstar
$ echo $?
1
```

这个用法主要用于脚本，供 `if` 条件结构使用

下面例子

```bash
# 如果打开了这个参数，就执行 if 结构内部的语句

if (shopt -q globstar); then
  ...
fi
```


# mktemp 命令，trap 命令

Bash 脚本有时需要创建临时文件或临时目录

常见的做法是，在 `/tmp` 目录里面创建文件或目录，这样做有很多弊端

使用 `mktemp` 命令是最安全的做法

## 临时文件的安全问题

直接创建临时文件，尤其在 `/tmp` 目录里面，往往会导致安全问题

1. `/tmp` 目录是所有人可读写的，创建的临时文件也是所有人可读的

```bash
$ touch /tmp/info.txt
$ ls -l /tmp/info.txt
-rw-r--r-- 1 ruanyf ruanyf 0 12月 28 17:12 /tmp/info.txt
```

上面命令在 `/tmp` 目录直接创建文件，该文件默认是所有人可读的

2. 如果攻击者知道临时文件的文件名，他可以创建符号链接，链接到临时文件，可能导致系统运行异常。攻击者也可能向脚本提供一些恶意数据。因此，临时文件最好使用不可预测、每次都不一样的文件名，防止被利用。

3. 临时文件使用完毕，应该删除。但是，脚本意外退出时，往往会忽略清理临时文件

生成临时文件应该遵循下面的规则

*   创建前检查文件是否已经存在
*   确保临时文件已成功创建
*   临时文件必须有权限的限制
*   临时文件要使用不可预测的文件名
*   脚本退出时，要删除临时文件（使用 `trap` 命令）

## mktemp 命令的用法

`mktemp` 命令就是为安全创建临时文件而设计的

- 在创建临时文件之前，它不会检查临时文件是否存在
- 它支持唯一文件名和清除机制，因此可以减轻安全攻击的风险

直接运行 `mktemp` 命令，就能生成一个临时文件

```bash
$ mktemp
/tmp/tmp.4GcsWSG4vj

$ ls -l /tmp/tmp.4GcsWSG4vj
-rw------- 1 ruanyf ruanyf 0 12月 28 12:49 /tmp/tmp.4GcsWSG4vj

# mktemp 命令生成的临时文件名是随机的，而且权限是只有用户本人可读写
```

Bash 脚本使用 `mktemp` 命令的用法如下

```bash
#!/bin/bash

TMPFILE=$(mktemp)
echo "Our temp file is $TMPFILE"
```

为了确保临时文件创建成功，`mktemp` 命令后面最好使用 `||` 运算符，保证创建失败时退出脚本

```bash
#!/bin/bash

TMPFILE=$(mktemp) || exit 1
echo "Our temp file is $TMPFILE"
```

为了保证脚本退出时临时文件被删除，可以使用 `trap` 命令指定退出时的清除操作

```bash
#!/bin/bash

trap 'rm -f "$TMPFILE"' EXIT # 见 trap 命令的用法

TMPFILE=$(mktemp) || exit 1
echo "Our temp file is $TMPFILE"
```

## mktemp 命令的参数

`-d` 参数可以创建一个临时目录

```bash
$ mktemp -d
/tmp/tmp.Wcau5UjmN6
```

`-p` 参数可以指定临时文件所在的目录

默认是使用 `$TMPDIR` 环境变量指定的目录，如果这个变量没设置，则使用 `/tmp` 目录

```bash
$ mktemp -p /home/ruanyf/
/home/ruanyf/tmp.FOKEtvs2H3
```

`-t` 参数可以指定临时文件的文件名模板

- 模板的末尾必须至少包含三个连续的 `X` 字符，表示随机字符，建议至少使用六个`X`
- 默认的文件名模板是 `tmp.` 后接十个随机字符

```bash
$ mktemp -t mytemp.XXXXXXX
/tmp/mytemp.yZ1HgZV
```

## trap 命令的用法

`trap` 命令用来在 Bash 脚本中响应系统信号

最常见的系统信号就是 SIGINT（中断），即按 `Ctrl + C` 所产生的信号

`trap` 的命令格式如下

```
$ trap [动作] [信号1] [信号2] ...
```

上面代码中，“动作” 是一个 Bash 命令，“信号” 常用的有以下几个

* EXIT：编号 0，这不是系统信号，而是 Bash 脚本特有的信号，不管什么情况，只要退出脚本就会产生
* HUP：编号 1，脚本与所在的终端脱离联系
* INT：编号 2，用户按下 Ctrl + C，意图让脚本终止运行
* QUIT：编号 3，用户按下 Ctrl + 斜杠，意图退出脚本
* KILL：编号 9，该信号用于杀死进程
* TERM：编号 15，这是 `kill` 命令发出的默认信号

`trap` 命令响应 `EXIT` 信号的写法如下

```bash
$ trap 'rm -f "$TMPFILE"' EXIT
```

上面语句的意思是：脚本遇到 `EXIT` 信号时，就会执行 `rm -f "$TMPFILE"`

trap 命令的常见使用场景，就是在 Bash 脚本中指定退出时执行的清理命令

```bash
#!/bin/bash

trap 'rm -f "$TMPFILE"' EXIT

TMPFILE=$(mktemp) || exit 1
ls /etc > $TMPFILE
if grep -qi "kernel" $TMPFILE; then
  echo 'find'
fi

# 这个例子有个严重的安全问题
# 要是 TMPFILE=$(mktemp) 执行失败，TMPFILE 为空串，继续执行到 || exit 1
# rm -f 岂不是要人命？
```

上面代码中，不管是脚本正常执行结束，还是用户按 Ctrl + C 终止，都会产生 `EXIT` 信号，从而触发删除临时文件

注意，`trap` 命令必须放在脚本的开头。否则，它上方的任何命令导致脚本退出，都不会被它捕获

如果 `trap` 需要触发多条命令，可以封装一个 Bash 函数

```bash
function egress {
  command1
  command2
  command3
}

trap egress EXIT
```

`trap` 命令的 `-l` 参数，可以列出所有的系统信号

```bash
$ trap -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX
```
