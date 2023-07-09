# 常用但记不住的命令

- 
- `su -` 切换到 root 账户
- `sudo !!` 扩展成 `sudo 上一次执行的命令`
- `cd -` 上一次进入的文件夹
- `man test` 比较运算符的 man page
- `shellcheck some.sh`
- `env` 命令或 `printenv` 命令，可以显示所有环境变量
- `set` 命令可以显示所有变量（包括环境变量和自定义变量），以及所有的 Bash 函数
- `<(someComend)` 会执行 `someComend` 并将结果输出到一个临时文件中，并将 `<( - CMD )` 替换成临时文件名

- 
- 敲 `^R` 后您可以输入子串来进行匹配，查找历史命令行
- `/dev/null` 黑洞文件
- `#!/usr/bin/env python` 在 `shebang` 行中使用 `env` 命令

- 
- `gcc -E as.c` 展开宏
- `gcc -c a.c` .c -> .o
- `objump -d a.o` .o -> .s