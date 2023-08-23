## tmux

*   **会话** - 每个会话都是一个独立的工作区，其中包含一个或多个窗口
    *   `tmux` 开始一个新的会话
    *   `tmux new -s NAME` 以指定名称开始一个新的会话
    *   `tmux ls` `<C-b> s` 列出当前所有会话 
    *   在 `tmux` 中输入 `<C-b> d` ，将当前会话分离
    *   `tmux a` 重新连接最后一个会话
    *   `tmux a -t NAME` 通过 `-t` 来指定具体的会话
    *   `<C-b> $` 重命名当前会话
    *   关闭会话
        *   `tmux rename -t s1 s2` 重命名会话s1为s2
        *   `tmux kill-session` 关闭上次打开的会话
        *   `tmux kill-session -t s1` 关闭会话s1
        *   `tmux kill-session -a -t s1` 关闭除s1外的所有会话
        *   `tmux kill-server` 关闭所有会话


*   **窗口** - 相当于编辑器或是浏览器中的标签页，从视觉上将一个会话分割为多个部分
    *   `<C-b> c` 创建一个新的窗口，使用 `<C-d>` 关闭
    *   `<C-b> N` 跳转到第 _N_ 个窗口，注意每个窗口都是有编号的
    *   `<C-b> p` 切换到前一个窗口
    *   `<C-b> n` 切换到下一个窗口
    *   `<C-b> ,` 重命名当前窗口
    *   `<C-b> w` 列出当前所有窗口

*   **面板** - 像 vim 中的分屏一样，面板使我们可以在一个屏幕里显示多个 shell
    *   `<C-b> "` 水平分割
    *   `<C-b> %` 垂直分割
    *   `<C-b> <方向>` 切换到指定方向的面板，<方向> 指的是键盘上的方向键
    *   `<C-b> z` 切换当前面板的缩放
    *   `<C-b> [` 开始往回卷动屏幕。您可以按下空格键来开始选择，回车键复制选中的部分
    *   `<C-b> <空格>` 在不同的面板排布间切换
    *   `<C-b> x` 关闭 

扩展阅读： 

[这里](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) 是一份 `tmux` 快速入门教程， [而这一篇](http://linuxcommand.org/lc3_adv_termmux.php) 文章则更加详细，它包含了 `screen` 命令

您也许想要掌握 [`screen`](https://www.man7.org/linux/man-pages/man1/screen.1.html) 命令，因为在大多数 UNIX 系统中都默认安装有该程序





