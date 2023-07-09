- [vim](#vim)
  - [Vim mode](#vim-mode)
  - [缓存， 标签页， 窗口](#缓存-标签页-窗口)
  - [Vim 的入门](#vim-的入门)
      - [Comand-line Mode](#comand-line-mode)
      - [移动（名词）](#移动名词)
      - [编辑（动词）](#编辑动词)
      - [选择](#选择)
      - [计数](#计数)
      - [修饰语](#修饰语)
  - [自定义 Vim](#自定义-vim)
  - [扩展 Vim](#扩展-vim)
  - [其他程序的 Vim 模式](#其他程序的-vim-模式)
      - [Shell](#shell)
      - [Readline](#readline)
      - [其他](#其他)
  - [Vim 进阶](#vim-进阶)
      - [搜索和替换](#搜索和替换)
      - [宏 macro](#宏-macro)
  - [扩展资料](#扩展资料)
  - [附录1](#附录1)

参考
- [The Missing Semester of Your CS Education:Vim](https://missing-semester-cn.github.io/2020/editors/)

# vim 

## Vim mode

它具有多种操作模式：

*   **Nomal**：移动光标
*   **Insert**
*   **Replace**
*   **Visual**：for selecting blocks of text
*   **Command-line**：用于执行命令

在不同的操作模式下，键盘敲击的含义也不同

在默认设置下，Vim 会在左下角显示当前的模式

通常你会把大部分时间花在正常模式和插入模式

在正常模式下键入

- `i` 进入 Insert 模式
- `R` 进入 Replace 模式
- `v` 进入 Visual 模式
- `V` 进入 Visual Line 模式
- `^v` 进入 Visual BLock 模式
- `:` 进入 Comand-line 模式

按下 `<ESC>` 从任何其他模式返回正常模式


## 缓存， 标签页， 窗口

打开的文件称为==缓存==

==窗口==本质上是做屏幕的分割，==窗口==里面可以载入不同的==缓存==

==标签==是容纳一整个屏幕的框架

[常用命令](https://www.cnblogs.com/jzy996492849/p/7156082.html)

*   用 `:sp` / `:vsp` 来分割窗口

## Vim 的入门

Vim 最重要的设计思想是 Vim 的界面本身是一个程序语言

键入就是命令，这些命令可以组合使用

#### Comand-line Mode

*   `:q` 退出（关闭窗口）
*   `:w` 保存（写）
*   `:wq` 保存然后退出
*   `:e {name of file}` 打开要编辑的文件
*   `:ls` 显示打开的缓存
*   `:help {topic}` 打开帮助文档
    *   `:help :w` 打开 `:w` 命令的帮助文档
    *   `:help w` 打开 `w` 移动的帮助文档

#### 移动（名词）

在 Normal 模式，使用移动命令在缓存中导航

在 Vim 里面移动也被称为 “名词”， 因为它们指向文字块

*   基本移动: `hjkl`
*   词： `w` （下一个词）， `b` （词初）， `e` （词尾），`* #` 光标所在单词跳跃
*   行： `0` （行初）， `^` （行初第一个非空格字符）， `$` （行尾）
*   屏幕： `H` （屏幕首行）， `M` （屏幕中间）， `L` （屏幕底部）
*   翻页： `Ctrl-u` （上翻）， `Ctrl-d` （下翻）
*   文件： `gg` （文件头）， `G` （文件尾）
*   行数： `:{number}` 或者 `{linenumber}G`
*   杂项： `%`，比如在 `(` 键入 `%` 跳转到 `)`
*   查找： `F{字符}`，`f{字符}`
    *   在本行查找 向前/向后 的{字符}
    *   `,` / `;` 用于导航匹配
*   搜索: `/{正则表达式}`, `n` / `N` 用于导航匹配

#### 编辑（动词）

Vim 的编辑命令也被称为 “动词”， 因为**动词可以施动于名词**

*   `r` 取代一个字符
*   `R` 光标所在位置取代
*   `i` 光标所在位置插入
*   `a` 在下一个位置插入
*   `O` / `o` 在之上/之下行插入
*   `d{移动命令}` 删除 {移动命令}
    *   例如，`dw` 删除词，`d$` 删除到行尾，`d0` 删除到行头。
    *   删除移动过程遇到的所有字符
*   `c{移动命令}` 改变 {移动命令}
    *   等价 `d{移动命令}` 再 `i`
*   `x` 删除字符（等价于 `dl`）
*   `s` 替换字符（等价于 `xi`）
*   Visual 模式 + 操作
    *   选中文字，`d` 删除 或者 `c` 改变
*   `u` 撤销，`<C-r>` 重做
*   `y`（yank）复制在可视模式下选中的文本（其他一些命令比如 `d` 也会复制）
*   `yy` 复制一行
*   `p` 粘贴
*   更多值得学习的: 比如 `~` 改变字符的大小写

#### 选择

Visual 模式:

*   可视化：`v`
*   可视化行： `V`
*   可视化块：`Ctrl+v`

然后用移动（“名词”）来选中

#### 计数

你可以用一个计数来结合“名词”和“动词”，这会执行指定操作若干次

*   `3w` 向前移动三个词
*   `5j` 向下移动5行
*   `7dw` 删除7个词

#### 修饰语

你可以用修饰语改变“名词”的意义。修饰语有 `i`，表示“内部”或者“在内“，和 `a`， 表示”周围“。

*   `ci(` 改变当前括号内的内容
*   `di[` 删除当前方括号内的内容
*   `da'` 删除当前单引号内的内容， 包括单引号本身


## 自定义 Vim

`cp /etc/vim/vimrc ~/.vimrc` 把配置文件移到用户目录下

常见配置选项见[附录1](#附录1)

你可以参考其他人的在 GitHub 上共享的设置文件
- 比如，授课人的 Vim 设置 ([Anish](https://github.com/anishathalye/dotfiles/blob/master/vimrc), [Jon](https://github.com/jonhoo/configs/blob/master/editor/.config/nvim/init.vim) (uses [neovim](https://neovim.io/)), [Jose](https://github.com/JJGO/dotfiles/blob/master/vim/.vimrc))

the Missing Semester 提供了一个设置修复了一些 Vim 默认设置奇怪行为

[点击这儿](https://missing-semester-cn.github.io/2020/files/vimrc)下载，然后将它保存成 `~/.vimrc`

## 扩展 Vim

Vim 有很多扩展插件

你可以使用内置的插件管理系统
- 创建一个 `~/.vim/pack/vendor/start/` 的文件
- 然后把插件放到这里（比如通过 `git clone`）

以下是一些推荐插件：

* [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim): 模糊文件查找
* [ack.vim](https://github.com/mileszs/ack.vim): 代码搜索
* [nerdtree](https://github.com/scrooloose/nerdtree): 文件浏览器
* [vim-easymotion](https://github.com/easymotion/vim-easymotion): 魔术操作

- 你可以查看讲师们的开源的配置文件 ([Anish](https://github.com/anishathalye/dotfiles), [Jon](https://github.com/jonhoo/configs), [Jose](https://github.com/JJGO/dotfiles)) 来看看使用的其他插件
- 浏览 [Vim Awesome](https://vimawesome.com/) 来了解一些很棒的插件
- 这个话题也有很多博客文章：搜索 “best Vim plugins”

## 其他程序的 Vim 模式

很多工具提供了 Vim 仿真

即在其他工具中用 Vim 的快捷键之类的

#### Shell

- Bash : `set -o vi`
- Zsh : `bindkey -v`
- Fish : `fish_vi_key_bindings`

另外，不管利用什么 shell，你可以 `export EDITOR=vim`，设置默认的编辑程序


#### Readline

Many programs use the [GNU Readline](https://tiswww.case.edu/php/chet/readline/rltop.html) library for their command-line interface.

Readline supports (basic) Vim emulation too, which can be enabled by adding the following line to the `~/.inputrc` file:

```
set editing-mode vi
```

With this setting, for example, the Python REPL will support Vim bindings.

#### 其他

There are even vim keybinding extensions for web [browsers](http://vim.wikia.com/wiki/Vim_key_bindings_for_web_browsers)，受欢迎的有
- 用于 Google Chrome 的 [Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=en)
- 用于 Firefox 的 [Tridactyl](https://github.com/tridactyl/tridactyl)
 
你甚至可以在 [Jupyter notebooks](https://github.com/lambdalisue/jupyter-vim-binding) 中用 Vim 快捷键

[这个列表](https://reversed.top/2016-08-13/big-list-of-vim-like-software) 中列举了支持类 vim 键位绑定的软件


## Vim 进阶

whenever you’re using your editor and you think “there must be a better way of doing this”, there probably is: look it up online.

#### 搜索和替换

`:s` (substitute)（[文档](http://vim.wikia.com/wiki/Search_and_replace)）

*   `%s/foo/bar/g`
    *   在整个文件中将 foo 全局替换成 bar
*   `%s/\[.*\](\(.*\))/\1/g`
    *   将有命名的 Markdown 链接替换成简单 URLs

#### 宏 macro

*   `q{character}` to start recording a macro in register `{character}`
*   `q` to stop recording
*   `@{字符}` replays the macro
*   `{number}@{character}` executes a macro `{number}` times
*   宏的执行遇错误会停止
*   宏可以递归
    *   首先用 `q{character}q` 清除宏
    *   录制该宏，用 `@{字符}` 来递归调用该宏 （在录制完成之前不会有任何操作）


*   例子：将 xml 转成 json ([file](https://missing-semester-cn.github.io/2020/files/example-data.xml))
    *   一个有 “name” / “email” 键对象的数组
    *   用一个 Python 程序？
    *   用 sed / 正则表达式
        *   `g/people/d`
        *   `%s/<person>/{/g`
        *   `%s/<name>\(.*\)<\/name>/"name": "\1",/g`
        *   …
    *   Vim 命令 / 宏
        *   `Gdd`, `ggdd` 删除第一行和最后一行
        *   格式化最后一个元素的宏 （寄存器 `e`）
            *   跳转到有 `<name>` 的行
            *   `qe^r"f>s": "<ESC>f<C"<ESC>q`
        *   格式化一个的宏
            *   跳转到有 `<person>` 的行
            *   `qpS{<ESC>j@eA,<ESC>j@ejS},<ESC>q`
        *   格式化一个标签然后转到另外一个的宏
            *   跳转到有 `<person>` 的行
            *   `qq@pjq`
        *   执行宏到文件尾
            *   `999@q`
        *   手动移除最后的 `,` 然后加上 `[` 和 `]` 分隔符

## 扩展资料

*   `vimtutor` 是一个 Vim 安装时自带的教程
*   [Vim Adventures](https://vim-adventures.com/) 是一个学习使用 Vim 的游戏
*   [Vim Tips Wiki](http://vim.wikia.com/wiki/Vim_Tips_Wiki)
*   [Vim Advent Calendar](https://vimways.org/2019/) 有很多 Vim 小技巧
*   [Vim Golf](http://www.vimgolf.com/) 是用 Vim 的用户界面作为程序语言的 [code golf](https://en.wikipedia.org/wiki/Code_golf)
*   [Vi/Vim Stack Exchange](https://vi.stackexchange.com/)
*   [Vim Screencasts](http://vimcasts.org/)
*   [Practical Vim](https://pragprog.com/titles/dnvim2/)（书籍）


## 附录1

```
setlocal noswapfile " 不要生成swap文件
set bufhidden=hide " 当buffer被丢弃的时候隐藏它
colorscheme evening " 设定配色方案
set number " 显示行号
set cursorline " 突出显示当前行
set ruler " 打开状态栏标尺
set shiftwidth=4 " 设定 << 和 >> 命令移动时的宽度为 4
set softtabstop=4 " 使得按退格键时可以一次删掉 4 个空格
set tabstop=4 " 设定 tab 长度为 4
set nobackup " 覆盖文件时不备份
set autochdir " 自动切换当前目录为当前文件所在的目录
set backupcopy=yes " 设置备份时的行为为覆盖
set hlsearch " 搜索时高亮显示被找到的文本
set noerrorbells " 关闭错误信息响铃
set novisualbell " 关闭使用可视响铃代替呼叫
set t_vb= " 置空错误铃声的终端代码
set matchtime=2 " 短暂跳转到匹配括号的时间
set magic " 设置魔术
set smartindent " 开启新行时使用智能自动缩进
set backspace=indent,eol,start " 不设定在插入状态无法用退格键和 Delete 键删除回车符
set cmdheight=1 " 设定命令行的行数为 1
set laststatus=2 " 显示状态栏 (默认值为 1, 无法显示状态栏)
set statusline=\ %<%F[%1*%M%*%n%R%H]%=\ %y\ %0(%{&fileformat}\ %{&encoding}\ Ln\ %l,\ Col\ %c/%L%) " 设置在状态行显示的信息
set foldenable " 开始折叠
set foldmethod=syntax " 设置语法折叠
set foldcolumn=0 " 设置折叠区域的宽度
setlocal foldlevel=1 " 设置折叠层数为 1
nnoremap <space> @=((foldclosed(line('.')) < 0) ? 'zc' : 'zo')<CR> " 用空格键来开关折叠

nmap Q <Nop> " Unbind Q
set mouse+=a " Enable mouse support.
set incsearch " searching as you type rather than waiting till you press enter.
```