- [Git 的数据模型](#git-的数据模型)
  - [快照 snapshots](#快照-snapshots)
  - [历史记录建模：关联快照](#历史记录建模关联快照)
  - [数据模型及其伪代码表示](#数据模型及其伪代码表示)
  - [对象 objects](#对象-objects)
  - [引用 references](#引用-references)
  - [仓库](#仓库)
  - [暂存区](#暂存区)
- [Git 的命令行接口](#git-的命令行接口)
  - [基础](#基础)
  - [分支和合并](#分支和合并)
  - [远端操作](#远端操作)
  - [撤销](#撤销)
- [Git 高级操作](#git-高级操作)
- [杂项](#杂项)
- [资源](#资源)

参考
- [The Missing Semester of Your CS Education:Git](https://missing-semester-cn.github.io/2020/version-control/)

---


尽管 Git 的接口有些丑陋，但是它的底层设计和思想却是非常优雅的

因此，我们将通过一种自底向上的方式向您介绍 Git

# Git 的数据模型

Git 拥有一个经过精心设计的模型，这使其能够支持版本控制所需的所有特性

## 快照 snapshots

Git 将顶级目录中的文件和文件夹作为集合，并通过一系列快照来管理其历史记录

在 Git 的术语里
- 文件被称作 Blob 对象（数据对象），也就是一组数据
- 目录则被称之为 “树”，它将名字与 Blob 对象或树对象进行映射（使得目录中可以包含其他目录）

快照则是被追踪的最顶层的树

例如，一个树看起来可能是这样的：

```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

这个顶层的树包含了两个元素，一个名为 “foo” 的树（它本身包含了一个 blob 对象 “bar.txt”），以及一个 blob 对象 “baz.txt”

## 历史记录建模：关联快照

在 Git 中，历史记录是一个由快照组成的有向无环图

注意，快照具有多个 “父辈” 而非一个，因为某个快照可能由多个父辈而来

- 例如，经过合并后的两条分支

在 Git 中，这些快照被称为 “commit”

通过可视化的方式来表示这些历史提交记录时，看起来差不多是这样的：

```
o <-- o <-- o <-- o
            ^  
             \
              --- o <-- o
```

其中的 `o` 表示一次 commit（快照）

在第三次提交之后，历史记录分岔成了两条独立的分支。这可能因为此时需要同时开发两个不同的特性，它们之间是相互独立的。开发完成后，这些分支可能会被合并并创建一个新的提交，这个新的提交会同时包含这些特性。新的提交会创建一个新的历史记录，看上去像这样（最新的合并提交用粗体标记）：

```
o <-- o <-- o <-- o <---- **o**
             ^             /
              \           v
               --- o <-- o
```

Git 中的提交是不可改变的，但这并不代表错误不能被修改，只不过这种 “修改” 实际上是创建了一个全新的提交记录

即下文中的 object 不可变，reference 可变

## 数据模型及其伪代码表示

以伪代码的形式来学习 Git 的数据模型，可能更加清晰：

```
// 文件就是一组数据
type blob = array<byte>

// 一个包含文件和目录的目录
type tree = map<string, tree | blob>

// 每个提交都包含一个父辈，元数据和顶层树
type commit = struct {
    parent: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

这是一种简洁的历史模型

## 对象 objects

Git 中的 object 可以是 blob、tree 或者 commit：

```
type object = blob | tree | commit
```

Git 在储存数据时，所有的对象都会基于它们的 [SHA-1 哈希](https://en.wikipedia.org/wiki/SHA-1) 进行寻址

```
# objects 为哈希表

objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

当它们引用其他对象时，它们并没有真正的在硬盘上保存这些对象，而是仅仅保存了它们的哈希值作为引用

例如

可以通过 `git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d` 来进行可视化上面例子中的树，看上去是这样的：

```
100644 blob 4448adbf7ecd394f42ae135bbeed9676e894af85    baz.txt
040000 tree c68d233a33c5c06e0340e4c224f0afca87c8ce87    foo
```

树本身会包含一些指向其他内容的指针

如果我们用 `git cat-file -p 4448adbf7ecd394f42ae135bbeed9676e894af85`，即通过哈希值查看 baz.txt 的内容，会得到以下信息：

```
git is wonderful
```

## 引用 references

所有的快照都可以通过它们的 SHA-1 哈希值来标记了

针对这一问题，Git 的解决方法是给这些哈希值赋予人类可读的名字，也就是引用

引用是指向提交的指针，与对象不同的是，它是可变的（引用可以被更新，指向新的提交）

例如，`master` 引用通常会指向主分支的最新一次提交

```
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

这样，Git 就可以使用诸如 “master” 这样人类可读的名称来表示历史记录中某个特定的提交，而不需要在使用一长串十六进制字符了

- `master` 一般指向最新版本
- 当前的位置有一个特殊的索引，它就是 `HEAD`

## 仓库

最后，我们可以粗略地给出 Git 仓库的定义了：`对象` 和 `引用`

在硬盘上，Git 仅存储对象和引用：因为其数据模型仅包含这些东西。所有的 `git` 命令都对应着对提交树的操作，例如增加对象，增加或删除引用。

当您输入某个指令时，请思考一下这条命令是如何对底层的图数据结构进行操作的。另一方面，如果您希望修改提交树，例如“丢弃未提交的修改和将 ‘master’ 引用指向提交 `5d83f9e` 时，有什么命令可以完成该操作（针对这个具体问题，您可以使用 `git checkout master; git reset --hard 5d83f9e`）

## 暂存区

考虑如下场景，您开发了两个独立的特性，然后您希望创建两个独立的提交，其中第一个提交仅包含第一个特性，而第二个提交仅包含第二个特性。或者，假设您在调试代码时添加了很多打印语句，然后您仅仅希望提交和修复 bug 相关的代码而丢弃所有的打印语句。

Git 处理这些场景的方法是使用一种叫做 “暂存区（staging area）” 的机制，它允许您指定下次快照中包括的那些改动

# Git 的命令行接口

强烈推荐您阅读 [Pro Git 中文版](https://git-scm.com/book/zh/v2)或观看本讲座的视频来学习

## 基础

*   `git help <command>`: 获取 git 命令的帮助信息
*   `git init`: 创建一个新的 git 仓库，其数据会存放在一个名为 `.git` 的目录下
*   `git status`: 显示当前的仓库状态
*   `git add <filename>`: 添加文件到暂存区
*   `git commit`: 创建一个新的提交
    *   [A Note About Git Commit Messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
    *   [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
*   `git log`: 显示历史日志
*   `git log --all --graph --decorate --oneline`: 可视化历史记录（有向无环图）
*   `git diff <filename>`: 显示 HEAD 与暂存区文件的差异
*   `git diff <revision1> <revision2> <filename>`: 显示某个文件两个版本之间的差异
*   `git checkout <revision>`: 更新 HEAD 和目前的分支
    *   `git checkout a.txt`: a.txt 的内容回到 HEAD 指向的地方

## 分支和合并

*   `git branch`: 显示分支
    *   `-vv`
*   `git branch <name>`: 创建分支
    *   `-d`: 删除
*   `git checkout -b <name>`: 创建分支并切换到该分支
    *   相当于 `git branch <name>; git checkout <name>`
*   `git merge <revision>`: 把 revision 合并到当前分支
*   `git mergetool`: 使用工具来处理合并冲突
*   `git rebase`: 将一系列补丁变基（rebase）为新的基线

## 远端操作

*   `git remote`: 列出远端
*   `git remote add <name> <url>`: 添加一个远端
*   `git push <remote> <local branch>:<remote branch>`: 将对象传送至远端并更新远端引用
*   `git branch --set-upstream-to=<remote>/<remote branch>`: 创建本地和远端分支的关联关系
*   `git fetch`: 从远端获取对象/索引
*   `git pull`: 相当于 `git fetch; git merge`
*   `git clone`: 从远端下载仓库

## 撤销


*   `git commit --amend`: 编辑提交的内容或信息
*   `git reset HEAD <file>`: 恢复暂存的文件
*   `git checkout -- <file>`: 丢弃修改
*   `git restore`: git2.32 版本后取代 `git reset` 进行许多撤销操作

# Git 高级操作


*   `git config`: Git 是一个 [高度可定制的](https://git-scm.com/docs/git-config) 工具
    *   或者 `vim ~/.gitconfig`
*   `git clone --depth=1`: 浅克隆（shallow clone），不包括完整的版本历史信息
*   `git add -p`: 交互式暂存
*   `git rebase -i`: 交互式变基
*   `git blame`: 查看最后修改某行的人
*   `git stash`: 暂时移除工作目录下的修改内容
    *   `git stash pop` 
*   `git bisect`: 通过二分查找搜索历史记录
*   `.gitignore`: [文档](https://git-scm.com/docs/gitignore)

杂项
==

*   **图形用户界面**: Git 的 [图形用户界面客户端](https://git-scm.com/downloads/guis) 有很多，但是我们自己并不使用这些图形用户界面的客户端，我们选择使用命令行接口
*   **Shell 集成**: 将 Git 状态集成到您的 shell 中会非常方便。([zsh](https://github.com/olivierverdier/zsh-git-prompt), [bash](https://github.com/magicmonty/bash-git-prompt))。[Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh)这样的框架中一般以及集成了这一功能
*   **编辑器集成**: 和上面一条类似，将 Git 集成到编辑器中好处多多。[fugitive.vim](https://github.com/tpope/vim-fugitive) 是 Vim 中集成 GIt 的常用插件
*   **工作流**: 我们已经讲解了数据模型与一些基础命令，但还没讨论到进行大型项目时的一些惯例 ( 有[很多](https://nvie.com/posts/a-successful-git-branching-model/) [不同的](https://www.endoflineblog.com/gitflow-considered-harmful) [处理方法](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow))
*   **GitHub**: Git 并不等同于 GitHub。 在 GitHub 中您需要使用一个被称作[拉取请求（pull request）](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)的方法来向其他项目贡献代码
*   **其他 Git 提供商**: GitHub 并不是唯一的。还有像 [GitLab](https://about.gitlab.com/) 和 [BitBucket](https://bitbucket.org/) 这样的平台

# 资源

- [Pro Git](https://git-scm.com/book/en/v2) ，**强烈推荐**！（[Pro Git 中文版](https://git-scm.com/book/zh/v2)）
    - 学习前五章的内容可以教会您流畅使用 Git 的绝大多数技巧
    - 后面的章节提供了很多有趣的高级主题
- [Oh Shit, Git!?!](https://ohshitgit.com/) ，简短的介绍了如何从 Git 错误中恢复
- [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/) ，简短的介绍了 Git 的数据模型，与本文相比包含较少量的伪代码以及大量的精美图片
- [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/) 详细的介绍了 Git 的实现细节，而不仅仅局限于数据模型
- [How to explain git in simple words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)
- [Learn Git Branching](https://learngitbranching.js.org/) 通过基于浏览器的游戏来学习 Git

