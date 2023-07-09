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
- [条件判断](#条件判断)
  - [if 结构](#if-结构)
  - [test 命令](#test-命令)
  - [判断表达式](#判断表达式)
      - [文件判断](#文件判断)
      - [字符串判断](#字符串判断)
      - [整数判断](#整数判断)
      - [正则判断](#正则判断)
      - [算术判断](#算术判断)
      - [test 判断的逻辑运算](#test-判断的逻辑运算)
      - [普通命令的逻辑运算](#普通命令的逻辑运算)
  - [case 结构](#case-结构)


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

`while`循环结合`shift`命令，也可以读取每一个参数

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

上面例子中，`while`循环不断执行`getopts 'lha:' OPTION`命令，每次执行就会读取一个连词线参数（以及对应的参数值），然后进入循环体。变量`OPTION`保存的是，当前处理的那一个连词线参数（即`l`、`h`或`a`）。如果用户输入了没有指定的参数（比如`-x`），那么`OPTION`等于`?`。循环体内使用`case`判断，处理这四种不同的情况。

如果某个连词线参数带有参数值，比如`-a foo`，那么处理`a`参数的时候，环境变量`$OPTARG`保存的就是参数值。

注意，只要遇到不带连词线的参数，`getopts`就会执行失败，从而退出`while`循环。比如，`getopts`可以解析`command -l foo`，但不可以解析`command foo -l`。另外，多个连词线参数写在一起的形式，比如`command -lh`，`getopts`也可以正确处理。

变量`$OPTIND`在`getopts`开始执行前是`1`，然后每次执行就会加`1`。等到退出`while`循环，就意味着连词线参数全部处理完毕。这时，`$OPTIND - 1`就是已经处理的连词线参数个数，使用`shift`命令将这些参数移除，保证后面的代码可以用`$1`、`$2`等处理命令的主参数。

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

```
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

```
if cd /path/to/somewhere; then
  rm *
else
  echo "Could not change directory! Aborting." 1>&2
  exit 1
fi
```

更简洁的写法是利用两个逻辑运算符 `&&` 和 `||`

```
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

所以，`source`命令执行脚本时，不需要`export`变量

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


# 条件判断

Bash 脚本的条件判断语法

## if 结构

`if` 是最常用的条件判断结构，它的语法如下

```
if commands; then
  commands
elif commands; then
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

## 判断表达式

`if` 关键字后面，跟的是一个命令

这个命令可以是 `test` 命令，也可以是其他命令

命令的返回值为 `0` 表示判断成立

常用的判断表达式有下面这些

#### 文件判断

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

#### 字符串判断

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

#### 整数判断

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

#### 正则判断

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

#### 算术判断

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

#### test 判断的逻辑运算

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

#### 普通命令的逻辑运算

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


`case`的匹配模式可以使用各种通配符，下面是一些例子

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