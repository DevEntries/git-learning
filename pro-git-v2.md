# pro-git.v2   

## 1 起步   

### 1.1 起步 - 关于版本控制   

#### 集中化的版本控制系统    
解决不同系统上的开发者协同工作。

最显而易见的缺点是***中央服务器的单点故障***。
如果中央服务器宕机一小时，那么这一个小时内，谁都无法提交更新，也就无法协同工作。
如果中心数据库所在的磁盘发生损坏，又没有做恰当备份，毫无疑问你将丢失所有数据。

#### 分布式版本控制系统    
客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。
每一次的克隆操作，实际上都是一次对代码仓库的完整备份。

对**非线性开发模式**的强力支持（允许成千上万个**并行开发**的分支）。

### 1.3 起步 - Git 基础   

#### 直接记录快照 而非差异比较    
概念上来区分，其它大部分系统以文件变更列表的方式存储信息。
这类系统（CVS、Subversion等）将它们保存的信息看作是
**一组基本文件和每个文件随时间逐步累积的差异**。

Git更像是把数据看作是对小型文件系统的一组快照。每次你提交更新，或在Git中保存项目状态时，
它主要对当时的全部文件**制作一个快照并保存这个快照的索引**。
为了高效，如果文件没有修改，Git不再重新存储该文件，而是只保留一个链接指向之前存储的文件。
Git对待数据更像是一个快照流。

#### Git 保证完整性    
Git中所有数据在存储前都计算校验和。
Git用以计算和校验的机制叫做**SHA-1散列**（hash，哈希）。

#### 三种状态   
Git有三种状态，你的文件可能处于其中之一：
已提交（committed）、已修改（modified）和已暂存（staged）。
已提交表示数据已经安全的保存在本地数据库中。
已修改表示修改了文件，但还没保存到数据库中。
已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。

由此引入 Git 项目的三个工作区域的概念：Git 仓库、工作目录以及暂存区域。

![git-1.3-three-areas](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-1.3-three-areas.jpg)

**暂存区域是一个文件**，保存了下次将提交的文件列表信息，一般在Git仓库目录中。 有时候也被称作“索引”。

### 1.6 起步 - 初次运行 Git 前的配置   

#### 用户信息    
```
git config --global user.name "John Doe" 
git config --global user.email johndoe@example.com 
```

#### 检查配置信息   
```
git config --list 
```
可键入`git config <key>`： 来检查 Git 的某一项配置。
```
git config user.name 
```

## 2 Git 基础   

### 2.1 Git 基础 - 获取 Git 仓库   

#### 在现有目录中初始化仓库    
如果你打算使用Git来对现有的项目进行管理，你只需要进入该项目目录并键入：
```
git init 
```
#### 克隆现有的仓库   
如果你想获得一份已经存在了的Git仓库的拷贝。
比如说，你想为某个开源项目贡献自己的一份力，这时就要用到`git clone`命令。

克隆仓库的命令格式是 `git clone [url]`。
```
git clone https://github.com/libgit2/libgit2
```
如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以使用如下命令：
```
git clone https://github.com/libgit2/libgit2 mylibgit
```

### 2.2 Git 基础 - 记录每次更新到仓库   

#### 记录每次更新到仓库   
请记住，你工作目录下的每一个文件都不外乎这两种状态：已跟踪或未跟踪。 
已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，
在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。 
工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，
它们既不存在于上次快照的记录中，也没有放入暂存区。 
初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

![git-2.2-file-status](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-2.2-file-status.jpg)

**Untracked files**   
未跟踪的文件意味着Git在之前的快照（提交）中没有这些文件。

#### 跟踪新文件   
使用命令`git add`开始跟踪一个文件。
`git add`命令使用**文件或目录的路径**作为参数；
如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

`git add`是个多功能命令：
可以用它开始跟踪新文件，
或者把已跟踪的文件放到暂存区，
还能用于合并时把有冲突的文件标记为已解决状态等。 
将这个命令理解为“**添加内容到下一次提交中**”而不是“将一个文件添加到项目中”要更加合适。

#### 忽略文件   
文件`.gitignore`的格式规范如下：
* 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配。
* 匹配模式可以以（/）开头防止递归。
* 匹配模式可以以（/）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以*在模式前加上惊叹号（!）取反*。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。
星号(\*) 匹配零个或多个任意字符；
问号（?）只匹配一个任意字符；
[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；
如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。
使用两个星号表示匹配任意中间目录，比如a/**/z 可以匹配 a/z, a/b/z 或 a/b/c/z等。

**MARK**    
GitHub 有一个十分详细的针对数十种项目及语言的 .gitignore 文件列表，你可以在[https://github.com/github/gitignore](https://github.com/github/gitignore) 找到它。

#### 查看已暂存和未暂存的修改   
要查看尚未暂存的文件更新了哪些部分，不加参数直接输入`git diff`。
此命令比较的是工作目录中当前文件和暂存区域快照之间的差异，
也就是修改之后还没有暂存起来的变化内容。

查看已经暂存起来的变化，可以用`git diff --cached`命令。(`--staged`同`--cached`)
注意，**`git diff`本身只显示尚未暂存的改动**，而不是自上次提交以来所做的所有改动。

#### 提交更新   
```
git commit -m "story 182: Fix benchmarks for speed"
```

#### 跳过暂存区域提交   
Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，
Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤。
```
git commit -a -m 'added new benchmarks'
```

#### 移除文件   
要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（**确切地说，是从暂存区域移除**），然后提交。
```
git rm README
git commit -m "delete README"
```

如果删除之前文件修改过并且已经放到暂存区域的话，则必须要用强制删除选项 `-f`。 这是一种安全特性。
用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。

另外一种情况是，我们想把文件从 Git 仓库中删除（亦即从暂存区域移除），但**仍然希望保留在当前工作目录中**。
换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。**使用 `--cached` 选项**
```
(use --cached to keep the file, or -f to force removal)
git rm --cached README
```

#### 移动文件   
要在 Git 中对文件重命名，可以这么做：
```
git mv README.md README
```
其实，运行 `git mv` 就相当于运行了下面三条命令：
```
mv README.md README
git rm README.md
git add README
```
如此分开操作，Git 也会意识到这是一次改名，所以不管何种方式结果都一样。
两者唯一的区别是，mv 是一条命令而另一种方式需要三条命令，直接用 `git mv` 轻便得多。

### 2.3 Git 基础 - 查看提交历史   

#### 查看提交历史   
完成这个任务最简单而又有效的工具是 `git log` 命令。
`--stat` 想看到每次提交的简略的统计信息。
`--pretty=oneline` **单行显示**历史提交信息。
`--abbrev-commit` 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。
`--graph` 显示 ASCII 图形表示的分支合并历史。

#### 限制输出长度   
`-<n>` 仅显示最近的 n 条提交
`--author` 仅显示指定作者相关的提交。
`--committer` 仅显示指定提交者相关的提交。

### 2.4 Git 基础 - 撤消操作   

#### 取消对文件的暂存   
```
(use "git reset HEAD <file>" to unstage)
```

#### 撤消对文件的修改   
如果你并不想保留对某文件的修改怎么办？ 
你该如何方便地撤消修改 - 将它**还原成上次提交时的样子**
（或者刚克隆完的样子，或者刚把它放入工作目录时的样子）？
```
(use "git checkout -- <file>" to discard changes in working directory)
```

**IMPORTANT**   
你需要知道 `git checkout -- [file]` 是一个危险的命令，这很重要。 
你对那个文件做的任何修改都会消失 - **你只是拷贝了另一个文件来覆盖它**。 
除非你确实清楚不想要那个文件了，否则不要使用这个命令。

### 2.5 Git 基础 - 远程仓库的使用   

#### 远程仓库的使用   
远程仓库是指托管在因特网或其他网络中的你的项目的版本库。

#### 查看远程仓库   
如果想查看你已经配置的远程仓库服务器，
可以运行 `git remote` 命令。 它会列出你指定的每一个远程服务器的简写。
也可以指定选项 `-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。
```
git remote -v
```

#### 添加远程仓库   
运行 `git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，
同时指定一个你可以轻松引用的简写。
```
git remote add pb https://github.com/paulboone/ticgit
```
现在你可以在命令行中使用字符串 pb 来代替整个 URL。
例如，如果你想拉取 Paul 的仓库中有但你没有的信息
```
git fetch pb
```

#### 从远程仓库中抓取与拉取   
就如刚才所见，从远程仓库中获得数据，可以执行`git fetch [remote-name]`
这个命令会访问远程仓库，从中*拉取所有你还没有的数据*。 
执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。

如果你使用 clone 命令克隆了一个仓库，命令会**自动将其添加为远程仓库**并默认以 “origin” 为简写。
所以，`git fetch origin` 会**抓取克隆（或上一次抓取）后**新推送的所有工作。

**IMPORTANT**   
必须注意 `git fetch` 命令会将数据拉取到你的本地仓库 - 它并**不会自动合并或修改你当前的工作**。 
当准备好时你必须手动将其合并入你的工作。

如果你有一个分支设置为跟踪一个远程分支，
可以使用 `git pull` 命令来自动的抓取然后合并远程分支到当前分支。 

#### 推送到远程仓库    
当你想分享你的项目时，必须将其推送到上游。 这个命令很简单：`git push [remote-name] [branch-name]`。
```
git push origin master
```

#### 查看远程仓库   
如果想要查看某一个远程仓库的更多信息，可以使用 `git remote show [remote-name]` 命令。
```
git remote show origin
```
它同样会列出远程仓库的 URL 与跟踪分支的信息。

* 执行 git push 会自动地推送到哪一个远程分支。
* 执行 git pull 时哪些分支会自动合并。

#### 远程仓库的移除与重命名   
如果想要重命名引用的名字可以运行 `git remote rename` 去修改一个远程仓库的简写名。
```
git remote rename pb paul
```
值得注意的是这同样也会修改你的远程分支名字。 那些过去引用 pb/master 的现在会引用 paul/master。

如果因为一些原因想要移除一个远程仓库，可以使用 `git remote rm`。
`git remote rm paul`

### 2.6 Git 基础 - 打标签   

#### 打标签   
像其他版本控制系统（VCS）一样，Git 可以给历史中的**某一个提交打上标签**，以示重要。
比较有代表性的是人们会使用这个功能来标记发布结点（v1.0 等）。

#### 列出标签    
在 Git 中列出已有的标签是非常简单直观的。
```
git tag
```

#### 附注标签    
在 Git 中创建一个附注标签是很简单的。 最简单的方式是当你在运行 tag 命令时指定 `-a` 选项。
```
git tag -a v1.4 -m 'my version 1.4'
```
-m 选项指定了一条将会存储在标签中的信息。

通过使用 `git show` 命令可以看到标签信息与对应的提交信息
```
git show v1.4
```

#### 后期打标签    
你也可以对过去的提交打标签。你需要在命令的末尾指定提交的校验和（或部分校验和）。
```
git tag -a v1.2 9fceb02 -m "my version v1.2"
```

#### 共享标签    
默认情况下，`git push` 命令并不会传送标签到远程仓库服务器上。
在创建完标签后你必须显式地推送标签到共享服务器上。你可以运行 `git push origin [tagname]`。
```
git push origin v1.5
```

如果想要一次性推送很多标签，也可以使用带有 `--tags` 选项。
```
git push origin --tags
```

#### 检出标签    
在 Git 中你并不能真的检出一个标签，因为它们并不能像分支一样来回移动。
如果你想要工作目录与仓库中特定的标签版本完全一样，
可以使用 `git checkout -b [branchname] [tagname]` 在特定的标签上创建一个新分支。
```
git checkout -b version2 v2.0.0
```

### 2.7 Git 基础 - Git 别名   

#### Git 别名   
Git 并不会在你输入部分命令时自动推断出你想要的命令。
如果不想每次都输入完整的 Git 命令，可以通过 `git config` 文件来轻松地为每一个命令设置一个别名。
```
git config --global alias.br branch
git config --global alias.st status
```

在创建你认为*应该存在的命令*时这个技术会很有用。
例如，为了解决取消暂存文件的易用性问题，可以向 Git 中添加你自己的取消暂存别名。
```
git config --global alias.unstage 'reset HEAD --'
```
可以看出，Git 只是简单地将别名替换为对应的命令。

## 3 Git 分支    
为何 Git 的分支模型如此出众呢？
Git 处理分支的方式可谓是难以置信的轻量，创建新分支这一操作几乎能在瞬间完成，
并且在不同分支之间的切换操作也是一样便捷。

### 3.1 Git 分支 - 分支简介   
为了真正理解 Git 处理分支的方式，我们需要回顾一下 Git 是如何保存数据的。

![git-3.1-commit-object-and-its-parent](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.1-commit-object-and-its-parent.jpg)

Git 的分支，其实**本质上仅仅是指向提交对象的可变指针**。
Git 的默认分支名字是 master。 在多次提交操作之后，
你其实已经有一个指向最后那个提交对象的 master 分支。
它会在每次的提交操作中自动向前移动。

![git-3.1-branch-and-commit-logs](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.1-branch-and-commit-logs.jpg)

**NOTE**   
Git 的 “master” 分支并不是一个特殊分支。 它就跟其它分支完全没有区别。

**IMPORTANT**   

1. 每次产生的提交对象会包含一个指向上次提交对象（**父对象**）的指针。(首次提交除外)   
2. Git 的分支，其实本质上**仅仅是指向提交对象的可变指针**。

#### 分支创建   
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

![git-3.1-whats-HEAD](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.1-whats-HEAD.jpg)

#### 分支切换   
要切换到一个已存在的分支，你需要使用 `git checkout` 命令。
```
git checkout testing
```
这样 HEAD 就指向 testing 分支了。

**MARK**   
**分支切换会改变你工作目录中的文件**   
在切换分支时，一定要注意你工作目录里的文件会被改变。
如果是切换到一个较旧的分支，你的工作目录会恢复到该分支最后一次提交时的样子。
如果 Git 不能干净利落地完成这个任务，它将禁止切换分支。

`--decorate -all` git log 命令使用这一功能参数，查看各个分支当前所指的对象

由于 Git 的分支实质上仅是包含所指对象校验和（长度为 40 的 SHA-1 值字符串）的文件，所以它的创建和销毁都异常高效。
创建一个新分支就像是往一个文件中写入 41 个字节（40 个字符和 1 个换行符），如此的简单能不快吗？

**前面提到git分支本质是可变指针，这里说实质是包含SHA-1校验和的文件，不过最终都是用来表示位置的**

由于每次提交都会记录父对象，所以寻找恰当的合并基础（译注：即共同祖先）也是同样的简单和高效。
这些高效的特性使得 Git 鼓励开发人员频繁地创建和使用分支。

### 3.2 Git 分支 - 分支的新建与合并   

#### 新建分支   
想要新建一个分支并同时切换到那个分支上，你可以运行一个带有 `-b` 参数的 `git checkout` 命令。
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

![git-3.2-new-hotfix-branch](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.2-new-hotfix-branch.jpg)

关于某个紧急问题的解决方案发布之后，你准备回到被打断之前时的工作中。 然而，
你应该先删除 hotfix 分支，因为你已经不再需要它了 —— master 分支已经指向了和hotfix同一个位置。
你可以使用带 `-d` 选项的 `git branch` 命令来删除分支。
```
git branch -d hotfix
```

#### 分支的合并   

![git-3.2-branch-deverged](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.2-branch-deverged.jpg)

Git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它。 
这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。

既然你的修改已经合并进来了，你已经不再需要 iss53 分支了。 
现在你可以在任务追踪系统中关闭此项任务，并删除这个分支。
```
git branch -d iss53
```

#### 遇到冲突时的分支合并   
如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们。

你可以在合并冲突后的任意时刻使用 `git status` 命令，
来查看那些因包含合并冲突而处于未合并（unmerged）状态的文件。

任何因包含合并冲突而有待解决的文件，都会以未合并状态标识出来。

在你解决了所有文件里的冲突之后，对每个文件使用 **`git add` 命令来将其标记为冲突已解决**。 
一旦暂存这些原本有冲突的文件，Git 就会将它们标记为冲突已解决。

### 3.3 Git 分支 - 分支管理   

#### 分支管理   
`git branch` 当前所有分支列表
`git branch -v` 查看每一个分支的最后一次提交
`git branch --merged` 查看哪些分支已经合并到当前分支
`git branch --no-merged` 查看尚未合并到当前分支的分支
`git branch -D` 如果真的想要删除分支并丢掉那些工作，可以使用 `-D` 选项强制删除它

### 3.4 Git 分支 - 分支开发工作流   

#### 长期分支   
因为 Git 使用简单的**三方合并**，所以就算在一段较长的时间内，反复把一个分支合并入另一个分支，也不是什么难事。

![git-3.4-branch-work-silos](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.4-branch-work-silos.jpg)

你可以用这种方法维护不同层次的稳定性。

#### 特性分支    
特性分支对任何规模的项目都适用。 特性分支是一种短期分支，它被用来**实现单一特性**工作。 

### 3.5 Git 分支 - 远程分支   

#### 远程分支   
**远程引用**是对远程仓库的引用（指针），包括分支、标签等等。

**远程跟踪分支**是**远程分支状态**的引用。 
它们是你不能移动的本地引用，当你做任何网络通信操作时，它们会自动移动。

它们以 `(remote)/(branch)` 形式命名。

**NOTE**   
**“origin” 并无特殊含义**   
远程仓库名字 “origin” 与分支名字 “master” 一样，在 Git 中并没有任何特别的含义一样。
“master” 是当你运行 git init 时**默认的起始分支名字**，原因仅仅是它的广泛使用。
“origin” 是当你运行 git clone 时**默认的远程仓库名字**。

![git-3.5-remote-and-local-branch](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.5-remote-and-local-branch.jpg)

也许，只要你不与 origin 服务器连接，你的 origin/master 指针就不会移动。

![git-3.5-someone-else-pushes](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.5-someone-else-pushes.jpg)

运行 `git fetch origin` 命令。 
这个会命令查找 “origin” 是哪一个服务器（在本例中，它是 `git.ourcompany.com`），
从中**抓取本地（库）没有的数据**，并且更新本地数据库，移动 `origin/master` 指针指向新的、更新后的位置。

![git-3.5-git-fetch.jpg](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.5-git-fetch.jpg)

你可以运行 `git remote add` 命令添加一个新的远程仓库引用到当前的项目。
将这个远程仓库命名为 teamone，将其作为整个 URL 的缩写。

![git-3.5-git-remote-add](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.5-git-remote-add.jpg)

现在，可以运行 `git fetch teamone` 来抓取远程仓库 teamone 有而本地没有的数据。

![git-3.5-fetch-teamxxx-master](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.5-fetch-teamxxx-master.jpg)

#### 推送   
如果希望和别人一起在名为 serverfix 的分支上工作，你可以像推送第一个分支那样推送它。 
运行 `git push (remote) (branch)`。
```
git push origin serverfix
```
这里有些工作被简化了。 
Git 自动将 serverfix 分支名字展开为 `refs/heads/serverfix:refs/heads/serverfix`，
那意味着，“推送本地的 serverfix 分支来更新远程仓库上的 serverfix 分支。”

你也可以运行 `git push origin serverfix:serverfix`，
它会做同样的事 - 相当于它说，“推送本地的 serverfix 分支，将其作为远程仓库的 serverfix 分支”。

可以通过这种格式来推送本地分支到一个命名不相同的远程分支。
可以运行 `git push origin serverfix:awesomebranch` 
来将本地的 serverfix 分支推送到远程仓库上的 awesomebranch 分支。

其他协作者从服务器上抓取数据时，
他们会在本地生成一个远程分支 `origin/serverfix`，
引用服务器的 serverfix 分支。
```
git fetch origin
```

要特别注意的一点是当**抓取到新的远程跟踪分支**时，本地不会自动生成一份可编辑的副本（拷贝）。 
换一句话说，这种情况下，不会有一个新的 serverfix 分支 - **只有一个不可以修改的 origin/serverfix 指针。**

**Keywords：合并到当前所在分支**   
可以运行 `git merge origin/serverfix` 将这些工作合并到当前所在的分支。

**Keywords：创建本地分支，起点是origin/serverfix**   
如果想要在自己的 serverfix 分支上工作，可以将其建立在远程跟踪分支之上。
```
git checkout -b serverfix origin/serverfix
```
这会给你一个用于工作的本地分支，并且**起点位于 origin/serverfix**。

#### 跟踪分支   
从一个**远程跟踪分支检出**一个本地分支会自动创建一个叫做 “跟踪分支”（有时候也叫做 “上游分支”）。 
跟踪分支是与远程分支有直接关系的**本地分支**。 
如果在一个跟踪分支上输入 git pull，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。
**Keywords: 远程跟踪分支检出 创建出的 本地分支，origin/master - 远程跟踪分支，master - 跟踪分支**   

当克隆一个仓库时，它通常会自动地创建一个跟踪 `origin/master` 的 master 分支。   
(当然，你可以随心所欲地设定为其它跟踪分支。)

如果你愿意的话可以设置其他的跟踪分支 - 其他远程仓库上的跟踪分支，或者不跟踪 master 分支。 两种方式如下： 

Git 提供了 `--track` 快捷方式。
```
git checkout --track origin/serverfix

ret: 
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix' #serverfix 和 origin/serverfix 同名
```

如果想要将本地分支与远程分支设置为不同名字。
```
git checkout -b sf origin/serverfix

ret: 
Branch sf set up to track remote branch serverfix from origin.
Switched to a new branch 'sf' # sf 和 origin/serverfix 不同名
```
现在，本地分支 sf 会自动从 `origin/serverfix` 拉取。

如果想要查看设置的所有跟踪分支，可以使用 `git branch` 的 `-vv` 选项。 
这会将所有的本地分支列出来并且包含更多的信息，如每一个分支正在跟踪哪个远程分支。
```
git branch -vv
```

#### 拉取     
当 `git fetch` 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。
`git pull` 在大多数情况下它的含义是一个 `git fetch` 紧接着一个 `git merge` 命令。
**NOTE**
由于 `git pull` 的魔法经常令人困惑所以通常单独显式地使用 fetch 与 merge 命令会更好一些。

#### 删除远程分支     
假设你已经通过远程分支做完所有的工作了 - 也就是说你和你的协作者已经完成了一个特性
并且将其合并到了远程仓库的 master 分支（或任何其他稳定代码分支）。
可以运行带有 `--delete` 选项的 git push 命令来删除一个远程分支。
如想要从服务器上删除 serverfix 分支，运行下面的命令。
```
git push origin --delete serverfix
```
基本上这个命令做的只是从服务器上移除这个指针。

### 3.6 Git 分支 - 变基   

#### 变基   
在 Git 中整合来自不同分支的修改主要有两种方法：merge 以及 rebase。 
在本节中我们将学习什么是“变基”，怎样使用“变基”，并将展示*该操作的惊艳之处*，
以及指出在*何种情况下你应避免使用它*。

之前介绍过，整合分支最容易的方法是 merge 命令。 
它会把两个分支的最新快照（C3 和 C4）以及二者最近的共同祖先（C2）进行三方合并，
合并的结果是生成一个新的快照（并提交）。

![git-3.6-git-merge](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.6-git-merge.jpg)

其实，还有一种方法：你可以提取在 C4 中引入的补丁和修改，然后在 C3 的基础上再应用一次。 

![git-3.6-git-rebase](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.6-git-rebase.jpg)

在 Git 中，这种操作就叫做 变基。
你可以使用 rebase 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。
```
git checkout experiment
git rebase master
```

![git-3.6-git-rebase-master](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.6-git-rebase-master.jpg)

现在回到 master 分支，进行一次快进合并。
```
git checkout master
git merge experiment
```

![git-3.6-git-merge-after-rebase](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-3.6-git-merge-after-rebase.jpg)

你在查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的，
但它们看上去就像是先后串行的一样，**提交历史是一条直线没有分叉**。

**IMPORTANT**   
请注意，无论是**通过变基**，还是**通过三方合并**，整合的最终结果所指向的快照始终是一样的，
只不过**提交历史不同**罢了。 
变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。

#### 变基的风险
呃，奇妙的变基也并非完美无缺，要用它得遵守一条准则：   
**不要对在你的仓库外有副本的分支执行变基**。   
如果你遵循这条金科玉律，就不会出差错。 否则，人民群众会仇恨你，你的朋友和家人也会嘲笑你，唾弃你。

## 4 服务器上的 Git   

到目前为止，你应该已经有办法使用 Git 来完成日常工作。 
然而，为了使用 Git 协作功能，你还需要有远程的 Git 仓库。

## 5 分布式 Git  

### 5.1 分布式 Git - 分布式工作流程    

#### 集中式工作流    
集中式系统中通常使用的是**单点协作模型**——集中式工作流。

![git-5.1-shared-repository](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-5.1-shared-repository.jpg)

#### 集成管理者工作流    
Git 允许多个远程仓库存在，使得这样一种工作流成为可能：   
**每个开发者拥有自己仓库的写权限和其他所有人仓库的读权限**。    
这种情形下通常会有个代表“官方”项目的权威的仓库。 要为这个项目做贡献，
你需要从该项目克隆出一个自己的公开仓库，然后将自己的修改推送上去。 
接着你可以请求**官方仓库的维护者**拉取更新合并到主项目。 
维护者可以将你的仓库作为远程仓库添加进来，在本地测试你的变更，将其合并入他们的分支并推送回官方仓库。

![git-5.1-blessed-repository](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-5.1-blessed-repository.jpg)

#### 司令官与副官工作流    
这其实是多仓库工作流程的变种。 一般拥有数百位协作开发者的超大型项目才会用到这样的工作方式，例如著名的 Linux 内核项目。 
被称为副官（lieutenant）的各个集成管理者分别负责**集成项目中的特定部分**。 
所有这些副官头上还有一位称为司令官（dictator）的**总集成管理者**负责统筹。 
**司令官维护的仓库作为参考仓库**，为所有协作者提供他们需要拉取的项目代码。

![git-5.1-dictator-lieutenant](https://raw.githubusercontent.com/huangrlm/markdown-img/master/git-5.1-dictator-lieutenant.jpg)

这种工作流程并*不常用*，只有当项目极为庞杂，或者需要多级别管理时，才会体现出优势。

## 6 GitHub   

##### 2016.9.17   
