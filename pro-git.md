# pro git

## 1. Git起步

**集中化的版本控制系统**
解决不同系统上的开发者协同工作。

最显而易见的缺点是中央服务器的单点故障。
如果中央服务器宕机一小时，那么这一个小时内，谁都无法提交更新，也就无法协同工作。
如果中心数据库所在磁盘发生损坏，有没有做备份，你将丢失所有数据。

**分布式版本控制系统**
客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。
每一次的克隆操作，实际上都是一次对代码仓库的完整备份。

更进一步，许多这类系统都可以指定和若干不同的远端代码仓库进行交互。籍此，你就可以在同一个项目中，分别和不同工作小组的人相互协作。

对**非线性开发模式**的强力支持（允许成千上万个**并行开发**的分支）。

**直接记录快照 而非差异比较**
 概念上来区分，其它大部分系统以文件变更列表的方式存储信息。这类系统（CVS、Subversion等）将它们保存的信息看作是**一组基本文件和每个文件随时间逐步累积的差异**。**基本文件和随时间逐步累积的差异**

Git更像是把数据看作是对小型文件系统的一组快照。每次你提交更新，或在Git中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。为了高效，如果文件没有修改，Git不再重新存储该文件，而是只保留一个链接指向之前存储的文件。Git对待数据更像是一个**快照流**。**文件快照和这个快照的索引**

**Git 保证完整性**
Git中所有数据在存储前都计算校验和。
Git用以计算和校验的机制叫做**SHA-1散列**（hash，哈希）。

**三种状态**
Git有三种状态，你的文件可能处于其中之一：已提交（committed）、已修改（modified）和已暂存（staged）。
已提交表示数据已经安全的保存在本地数据库中。
已修改表示修改了文件，但还没保存到数据库中。
已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。**对已修改的文件做标记**
![git-directory](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-directory.png)

**Staging Area**
**暂存区域是一个文件**，保存了下次将提交的文件列表信息，一般在Git仓库目录中。 有时候也被称作“索引”。

**用户信息**
```
git config --global user.name "John Doe" #设置用户名
git config --global user.email johndoe@example.com #设置邮箱
```

检查配置信息
```
git config --list #查看配置信息
```
可键入git config <key>： 来检查 Git 的某一项配置。
```
git config user.name #查看某一项配置
```

## 2. Git基础

**在现有目录中初始化仓库**
如果你打算使用Git来对现有的项目进行管理，你只需要进入该项目目录并键入：
```
git init #初始化仓库
```

**克隆现有的仓库**
如果你想获得一份已经存在了的Git仓库的拷贝，比如说，你想为某个开源项目贡献自己的一份力，这时就要用到`git clone`命令。

克隆仓库的命令格式是 `git clone [url]`。

**记录更新到仓库**
请记住，你工作目录下的每一个文件都不外乎这两种状态：已跟踪或未跟踪。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。 工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。
![git-status](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-status.png)

**Untracked files**
未跟踪的文件意味着Git在之前的快照（提交）中没有这些文件。

**跟踪新文件**
使用命令`git add`开始跟踪一个文件。
`git add`命令使用**文件或目录的路径**作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

`git add`是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。 将这个命令理解为“**添加内容到下一次提交中**”而不是“将一个文件添加到项目中”要更加合适。

**忽略文件**
文件`.gitignore`的格式规范如下：
* 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配。
* 匹配模式可以以（/）开头防止递归。
* 匹配模式可以以（/）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。
星号(\*) 匹配零个或多个任意字符；
问号（?）只匹配一个任意字符；
[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；
如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。
使用两个星号表示匹配任意中间目录，比如a/**/z 可以匹配 a/z, a/b/z 或 a/b/c/z等。

**TIP**
GitHub 有一个十分详细的针对数十种项目及语言的 .gitignore 文件列表，你可以在[https://github.com/github/gitignore](https://github.com/github/gitignore) 找到它。

**查看已暂存和未暂存的修改**
要查看尚未暂存的文件更新了哪些部分，不加参数直接输入`git diff`
此命令比较的是工作目录中当前文件和暂存区域快照之间的差异， 也就是修改之后还没有暂存起来的变化内容。

查看已经暂存起来的变化，可以用`git diff --cached`命令。(`--staged`同`--cached`)
注意，`git diff`本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。

**提交更新**
```
git commit -m "story 182: Fix benchmarks for speed"
```

**跳过暂存区域提交**
Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 git commit 加上 `-a` 选项，
Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add 步骤。
```
git commit -a -m 'added new benchmarks'
```

**移除文件**
要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（**确切地说，是从暂存区域移除**），然后提交。
```
git rm README
git commit -m "delete README"
```

如果删除之前**文件修改过并且已经放到暂存区域**的话，则必须要用强制删除选项 `-f`。 这是一种安全特性。
用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。
换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。**使用 --cached 选项**
```
(use --cached to keep the file, or -f to force removal)
git rm --cached README

# 备注:
# rm - From 仓库
# --cached - From 暂存区
```
当你忘记添加 .gitignore 文件，不小心把一个很大的日志文件或一堆 .a 这样的编译生成文件添加到暂存区时，这一做法尤其有用。

**移动文件**
要在 Git 中对文件重命名，可以这么做：
```
git mv README.md README
```
其实，运行 git mv 就相当于运行了下面三条命令：
```
mv README.md README
git rm README.md
git add README
```
如此分开操作，Git 也会意识到这是一次改名，所以不管何种方式结果都一样。
两者唯一的区别是，mv 是一条命令而另一种方式需要三条命令，直接用 git mv 轻便得多。

**查看提交历史**
`--stat` 想看到每次提交的简略的统计信息。
`--pretty=oneline` **单行显示**历史提交信息。
`--abbrev-commit` 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。
`--graph` 显示 ASCII 图形表示的分支合并历史。

**限制输出长度**
`-<n>` 仅显示最近的 n 条提交
`--author` 仅显示指定作者相关的提交。
`--committer` 仅显示指定提交者相关的提交。

**取消对文件的暂存**
```
(use "git reset HEAD <file>" to unstage)
```
*NOTE*
虽然在调用时加上 --hard 选项可以令 git reset 成为一个危险的命令。（译注：可能导致工作目录中所有当前进度丢失！）

**撤消对文件的修改**
如果你并不想保留对某文件的修改怎么办？ 你该如何方便地撤消修改 - 将它**还原成上次提交时的样子**
（或者刚克隆完的样子，或者刚把它放入工作目录时的样子）？
```
(use "git checkout -- <file>" to discard changes in working directory)
```
*IMPORTANT*
你需要知道 git checkout -- [file] 是一个危险的命令，这很重要。 你对那个文件做的任何修改都会消失
 **你只是拷贝了另一个文件来覆盖它**。

**远程仓库的使用**
远程仓库是指托管在因特网或其他网络中的你的项目的版本库。

**查看远程仓库**
如果想查看你已经配置的远程仓库服务器，可以运行 git remote 命令。 它会列出你指定的每一个远程服务器的简写。

也可以指定选项 -v，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。
```
git remote -v
```

**打标签**
像其他版本控制系统（VCS）一样，Git 可以给历史中的某一个提交打上标签，以示重要。
比较有代表性的是人们会使用这个功能来标记发布结点（v1.0 等）。

**列出标签**
在 Git 中列出已有的标签是非常简单直观的。
```
git tag
```

**附注标签**
在 Git 中创建一个附注标签是很简单的。 最简单的方式是当你在运行 tag 命令时指定 -a 选项。
```
 git tag -a v1.4 -m 'my version 1.4'
```
-m 选项指定了一条将会存储在标签中的信息。

通过使用 git show 命令可以看到标签信息与对应的提交信息
```
git show v1.4
```

**后期打标签**
你也可以对过去的提交打标签。你需要在命令的末尾指定提交的**校验和**（或部分校验和）。
```
git tag -a v1.2 9fceb02 -m "my version v1.2"
```

**共享标签**
默认情况下，git push 命令并不会传送标签到远程仓库服务器上。
在创建完标签后你必须显式地推送标签到共享服务器上。你可以运行 `git push origin [tagname]`。
```
git push origin v1.5
```

如果想要一次性推送很多标签，也可以使用带有 --tags 选项。
```
git push origin --tags
```

**检出标签**
在 Git 中你并不能真的检出一个标签，因为它们并不能像分支一样来回移动。
如果你想要工作目录与仓库中特定的标签版本完全一样，
可以使用 `git checkout -b [branchname] [tagname]` 在特定的标签上创建一个新分支。
```
git checkout -b version2 v2.0.0
```

**Git 别名**
Git 并不会在你输入部分命令时自动推断出你想要的命令。
如果不想每次都输入完整的 Git 命令，可以通过 git config 文件来轻松地为每一个命令设置一个别名。
```
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

在创建你认为应该存在的命令时这个技术会很有用。
例如，为了解决取消暂存文件的易用性问题，可以向 Git 中添加你自己的取消暂存别名。
```
git config --global alias.unstage 'reset HEAD --'
```
可以看出，Git 只是简单地将别名替换为对应的命令。

## 3. Git分支
为何 Git 的分支模型如此出众呢？
Git 处理分支的方式可谓是难以置信的轻量，创建新分支这一操作几乎能在瞬间完成，
并且在不同分支之间的切换操作也是一样便捷。

### 3.1 分支简介
为了真正理解 Git 处理分支的方式，我们需要回顾一下 Git 是如何保存数据的。
![git-commit-object](https://raw.githubusercontent.com/huangrlm/markdown-img/ed924903d8936d76f69ed67383280ae1d2f173f1/git-commit-object.png)
**注意其中parent变化**

Git 的分支，其实**本质上仅仅是指向提交对象的可变指针**。
Git 的默认分支名字是 master。 在多次提交操作之后，你其实已经有一个指向最后那个提交对象的 master 分支。
它会在每次的提交操作中自动向前移动。
![git-branch-and-commit-logs](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-branch-and-commit-logs.png)

*NOTE*
Git 的 “master” 分支并不是一个特殊分支。 它就跟其它分支完全没有区别。

**IMPORTANT** - <可以理解为一个单向链表和头指针>
1. 每次产生的提交对象会包含一个指向上次提交对象（**父对象**）的指针。(首次提交除外)
2. Git 的分支，其实本质上仅仅是**指向提交对象的可变指针**。

**分支创建**
Git 是怎么创建新分支的呢？ 很简单，它只是为你创建了一个可以移动的新的指针。 比如
```
git branch testing
```
这会在当前所在的提交对象上创建一个指针。

那么，Git 又是怎么知道当前在哪一个分支上呢？ 也很简单，它有一个名为 `HEAD` 的特殊指针。
请注意它和许多其它版本控制系统（如 Subversion 或 CVS）里的 HEAD 概念完全不同。
在 Git 中，它是一个指针，指向当前所在的本地分支（**译注：将 HEAD 想象为当前分支的别名**）。

**IMPORTANT**
`HEAD`可以想象为分支别名，既然是别名，就必须注意它引用的是哪个分支。
![git-branch-HEAD](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-branch-HEAD.png)

**分支切换**
要切换到一个已存在的分支，你需要使用 git checkout 命令。
```
git checkout testing
```
这样 HEAD 就指向 testing 分支了。

**NOTE: 分支切换会改变你工作目录中的文件**
在切换分支时，一定要注意你工作目录里的文件会被改变。
如果是切换到一个较旧的分支，你的工作目录会恢复到该分支最后一次提交时的样子。
如果 Git 不能干净利落地完成这个任务，它将禁止切换分支。

`--decorate -all` git log 命令使用这一功能参数，查看各个分支当前所指的对象


由于 Git 的分支实质上仅是包含所指对象校验和（长度为 40 的 SHA-1 值字符串）的文件，所以它的创建和销毁都异常高效。
创建一个新分支就像是往一个文件中写入 41 个字节（40 个字符和 1 个换行符），如此的简单能不快吗？
*前面提到git分支本质是可变指针，这里说实质是包含SHA-1校验和的文件 --- 都是用来表示位置的*

由于每次提交都会记录父对象，所以寻找恰当的合并基础（译注：即共同祖先）也是同样的简单和高效。
这些高效的特性使得 Git 鼓励开发人员频繁地创建和使用分支。


### 3.2 分支的新建与合并
**新建分支**
想要新建一个分支并同时切换到那个分支上，你可以运行一个带有 -b 参数的 git checkout 命令。
```
git checkout -b iss53
```
它是下面两条命令的简写：
```
git branch iss53
git checkout iss53
```
请牢记：当你切换分支的时候，Git 会重置你的工作目录，
使其看起来像回到了**你在那个分支上最后一次提交的样子**。 
Git 会自动添加、删除、修改文件，
以确保此时你的工作目录和这个分支最后一次提交时的样子一模一样。
![git-hotfix-branch](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-hotfix-branch.png)

关于某个紧急问题的解决方案发布之后，你准备回到被打断之前时的工作中。 然而，
你应该先删除 hotfix 分支，因为你已经不再需要它了 —— master 分支已经指向了和hotfix同一个位置。
你可以使用带 -d 选项的 git branch 命令来删除分支。
```
git branch -d hotfix
```

**分支的合并**
![git-merge-diverged](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-merge-diverged.png)
Git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它。 这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。

既然你的修改已经合并进来了，你已经不再需要 iss53 分支了。 
现在你可以在任务追踪系统中关闭此项任务，并删除这个分支。
```
git branch -d iss53
```

**遇到冲突时的分支合并**
如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们。

你可以在合并冲突后的任意时刻使用 git status 命令，
来查看那些因包含合并冲突而处于未合并（unmerged）状态的文件。

任何因包含合并冲突而有待解决的文件，都会以未合并状态标识出来。

在你解决了所有文件里的冲突之后，对每个文件使用 **git add 命令来将其标记为冲突已解决**。 
一旦暂存这些原本有冲突的文件，Git 就会将它们标记为冲突已解决。

### 3.3 分支管理
```
git branch 当前所有分支列表
git branch -v 查看每一个分支的最后一次提交
git branch --merged 查看哪些分支已经合并到当前分支
git branch --no-merged 查看尚未合并到当前分支的分支
git branch -D 如果真的想要删除分支并丢掉那些工作，可以使用 -D 选项强制删除它
```

### 3.4 分支开发工作流
**长期分支**
因为 Git 使用简单的**三方合并**，所以就算在一段较长的时间内，反复把一个分支合并入另一个分支，也不是什么难事。
![git-work-silos](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-work-silos.png)
你可以用这种方法维护不同层次的稳定性。

**特性分支**
特性分支对任何规模的项目都适用。 特性分支是一种短期分支，它被用来实现单一特性工作。 

### 3.5 远程分支
远程引用是对远程仓库的引用（指针），包括分支、标签等等。
```
git ls-remote (remote) 显式地获得远程引用的完整列表
git remote show (remote) 获得远程分支的更多信息
```

远程跟踪分支是远程分支状态的引用。
它们以 (remote)/(branch) 形式命名。

*NOTE*
**“origin” 并无特殊含义**
远程仓库名字 “origin” 与分支名字 “master” 一样，在 Git 中并没有任何特别的含义一样。 
同时 “master” 是当你运行 git init 时**默认的起始分支名字**。
“origin” 是当你运行 git clone 时**默认的远程仓库名字**。

// png
也许，只要你不与 origin 服务器连接，你的 `origin/master` 指针就不会移动。
// png
如果要同步你的工作，运行 `git fetch origin` 命令。
 这个命令查找 “origin” 是哪一个服务器（在本例中，它是 git.ourcompany.com），从中**抓取本地没有的数据**，
并且更新本地数据库，移动 origin/master 指针指向新的、更新后的位置。

**推送**
如果希望和别人一起在名为 serverfix 的分支上工作，你可以像推送第一个分支那样推送它。 
运行 git push (remote) (branch)。
```
git push origin serverfix
```


你也可以运行 `git push origin serverfix:serverfix`，它会做同样的事。
相当于它说，“推送本地的 serverfix 分支，将其作为远程仓库的 serverfix 分支”，
可以通过这种格式来推送本地分支到一个命名不相同的远程分支。

其他协作者从服务器上抓取数据时，他们会在本地生成一个远程分支 origin/serverfix，
指向服务器的 serverfix 分支的引用。

要特别注意的一点是当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本（拷贝）。 
换一句话说，这种情况下，不会有一个新的 serverfix 分支 - 只有一个**不可以修改**的 origin/serverfix 指针。

可以运行 git merge origin/serverfix 将这些工作合并到当前所在的分支。

如果想要在自己的 serverfix 分支上工作，可以将其建立在远程跟踪分支之上。
```
git checkout -b serverfix origin/serverfix
``` 
这会给你一个用于工作的本地分支，并且起点位于 origin/serverfix。

**跟踪分支**
跟踪分支
从一个远程跟踪分支检出一个本地分支会自动创建一个叫做 “跟踪分支”（有时候也叫做 “上游分支”）。 
**跟踪分支是与远程分支有直接关系的本地分支**。 
如果在一个跟踪分支上输入 git pull，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。

当克隆一个仓库时，它通常会自动地创建一个跟踪 origin/master 的 master 分支。 
然而，如果你愿意的话可以设置其他的跟踪分支 - 其他远程仓库上的跟踪分支，或者不跟踪 master 分支。

最简单的就是之前看到的例子，运行 `git checkout -b [branch] [remotename]/[branch]`。

Git 提供了 --track 快捷方式。
```
git checkout --track origin/serverfix
```
如果想要将本地分支与远程分支设置为不同名字。
```
git checkout -b sf origin/serverfix
```


如果想要查看设置的所有跟踪分支，可以使用 git branch 的 -vv 选项。 这会将所有的本地分支列出来并且包含更多的信息。
```
git branch -vv
```
// TODO: 

## 6 GitHub 