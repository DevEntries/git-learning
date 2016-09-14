## 3 Git 分支   

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
![git-3.6-git-merge]()

其实，还有一种方法：你可以提取在 C4 中引入的补丁和修改，然后在 C3 的基础上再应用一次。 
![git-3.6-git-rebase]()
在 Git 中，这种操作就叫做 变基。
你可以使用 rebase 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。
```
git checkout experiment
git rebase master
```
![git-3.6-git-rebase-master]()

现在回到 master 分支，进行一次快进合并。
```
git checkout master
git merge experiment
```
![git-3.6-git-merge-after-rebase]()

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
![git-5.1-shared-repository]()

#### 集成管理者工作流    
Git 允许多个远程仓库存在，使得这样一种工作流成为可能：   
**每个开发者拥有自己仓库的写权限和其他所有人仓库的读权限**。    
这种情形下通常会有个代表“官方”项目的权威的仓库。 要为这个项目做贡献，
你需要从该项目克隆出一个自己的公开仓库，然后将自己的修改推送上去。 
接着你可以请求**官方仓库的维护者**拉取更新合并到主项目。 
维护者可以将你的仓库作为远程仓库添加进来，在本地测试你的变更，将其合并入他们的分支并推送回官方仓库。
![git-3.1-blessed-repository]()

#### 司令官与副官工作流    
这其实是多仓库工作流程的变种。 一般拥有数百位协作开发者的超大型项目才会用到这样的工作方式，例如著名的 Linux 内核项目。 
被称为副官（lieutenant）的各个集成管理者分别负责**集成项目中的特定部分**。 
所有这些副官头上还有一位称为司令官（dictator）的**总集成管理者**负责统筹。 
**司令官维护的仓库作为参考仓库**，为所有协作者提供他们需要拉取的项目代码。
![git-5.1-dictator-lieutenant]()
这种工作流程并*不常用*，只有当项目极为庞杂，或者需要多级别管理时，才会体现出优势。

## 6 GitHub   


