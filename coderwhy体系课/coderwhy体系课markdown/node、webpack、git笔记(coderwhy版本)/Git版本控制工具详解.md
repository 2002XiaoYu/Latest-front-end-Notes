# Git版本控制工具详解

## (理解)认识版本控制和提供的功能

> ### 作者：小余同学
>
> 为什么没有01，那是因为01是讲谷歌V8引擎的原理，我看完了，但忘记写笔记了，不想重新看一遍写笔记了hh，大致来说也是很重要的一章节
>
> 需要全系列笔记请到[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)中自行获取，觉得不错给个star，这是对作者非常大的鼓励

### 认识版本控制（版本控制）

- **什么是版本控制？**
  - 版本控制的英文是**Version control**
  - 是维护**工程蓝图**的标准作法，能**追踪工程蓝图从诞生一直到定案的过程**
  - 版本控制也是**一种软件工程技巧**，借此能在软件开发的过程中，**确保由不同人所编辑的同一程序文件都得到同步**
- **简单来说，版本控制在软件开发中，可以帮助程序员进行代码的追踪、维护、控制等等一系列的操作**

![image-20230220053919667](.\node_webpack_git_image\image-20230220053919667.png)

### 版本控制的功能

- **对于我们日常开发，我们常常面临如下一些问题，通过版本控制可以很好的解决：**
- **不同版本的存储管理：**
  - 一个项目会不断进行版本的迭代，来修复之前的一些问题、增加新的功能、需求，甚至包括项目的重构
  - 如果我们通过手动来维护一系列的项目备份，简直是一场噩梦
- **重大版本的备份维护：**
  - 对于很多重大的版本，我们会进行备份管理
- **恢复之前的项目版本：**
  - 当我们开发过程中发生一些严重的问题时，想要恢复之前的操作或者回到之前某个版本
- **记录项目的点点滴滴：**
  - 如果我们每一个功能的修改、bug的修复、新的需求更改都需要记录下来，版本控制可以很好的解决
- **多人开发的代码合并：**
  - 项目中通常都是多人开发，将多人代码进行合并，并且在出现冲突时更好的进行处理

## (了解)版本控制的历史和Git的诞生

### 版本控制的历史

- **版本控制的史前时代（没有版本控制）：**
  - 人们通常通过文件备份的方式来进行管理，再通过diff命令来对比两个文件的差异
- **CVS（Concurrent Versions System）**
  - **第一个被大规模使用的版本控制工具**，诞生于1985年
  - 由荷兰阿姆斯特丹VU大学的Dick Grune教授实现的，也算是SVN的前身（SVN的出现就是为了取代CVS的）
- **SVN（Subversion）**
  - 因其命令行工具名为svn因此通常被简称为SVN
  - SVN由CollabNet公司于2000年资助并发起开发，目的是取代CVS，对CVS进行了很多的优化
  - SVN和CVS一样，也属于**集中式版本控制工具**
  - SVN在**早期公司开发中使用率非常高**，但是**目前已经被Git取代**
- **Git（Linus的作品）**
  - 早期的时候，Linux社区使用的是**BitKeeper来进行版本控制**
  - 但是因为一些原因，**BitKeeper想要收回对Linux社区的免费授权**
  - 于是Linus用了**大概一周**的时间，开发了**Git用来取代BitKeeper**
  - Linus完成了Git的核心设计，在之后Linus功成身退，将Git交由另外一个Git的主要贡献者Junio C Hamano来维护

### 集中式版本控制

- **CVS和SVN都是是属于集中式版本控制系统（Centralized Version Control Systems，简称 CVCS）**
  - 它们的主要特点是单一的**集中管理的服务器，保存所有文件的修订版本**
  - 协同开发人员通过客户端**连接到这台服务器，取出最新的文件或者提交更新**

![image-20230220130303567](.\node_webpack_git_image\image-20230220130303567.png)

- **这种做法带来了许多好处，特别是相较于老式的本地管理来说，每个人都可以在一定程度上看到项目中的其他人正在做些什么**
- **但是集中式版本控制也有一个核心的问题：中央服务器不能出现故障：**
  - 如果宕机一小时，那么在这一小时内，谁都无法提交更新，也就无法协同工作
  - 如果中心数据库所在的磁盘发生损坏，又没有做恰当备份，毫无疑问你将丢失所有数据

## (理解)集中式和分布式版本控制的区别

### 分布式版本控制

- **Git是属于分布式版本控制系统（Distributed Version Control System，简称 DVCS）**
  - 客户端并不只提取最新版本的文件快照， 而是**把代码仓库完整地镜像下来，包括完整的历史记录**
  - 这么一来，任何一处协同工作用的**服务器发生故障**，事后都**可以用任何一个镜像出来的本地仓库恢复**
  - 因为每一次的克隆操作，实际上都是一次**对代码仓库的完整备份**
- **目前在公司开发中我们都是使用Git来管理项目的**

![image-20230220130455173](.\node_webpack_git_image\image-20230220130455173.png)

## (掌握)Git的安装和安装内容的说明

### Git的安装

- **电脑上要想使用Git，我们需要先对Git进行安装：**
  - Git的官网：https://git-scm.com/downloads
  - 根据自己的操作系统下载Git即可；
- **在window操作系统按照默认配置全局安装即可**

### Bash – CMD – GUI 区别

- **Bash，Unix shell 的一种，Linux 与 Mac OS X 都将它作为默认 shell**
  - Git Bash 就是一个 shell，是 **Windows 下的命令行工具**，可以**执行 Linux 命令**
  - Git Bash 是**基于 CMD** 的，在 **CMD 的基础上增添一些新的命令与功能**
  -  所以建议在使用的时候，**用 Bash 更加方便**
- **Git CMD**
  - 命令行提示符（CMD）是 **Windows 操作系统上的命令行解释程序**
  - 当你在 Windows 上安装 git 并且习惯使用命令行时，可以使用 cmd 来运行 git 命令
- **Git GUI**
  - 基本上针对那些不喜欢黑屏（即命令行）编码的人
  - 它提供了一个**图形用户界面**来运行 git 命令

### Git的配置分类

- **既然已经在系统上安装了 Git，你会需要做几件事来定制你的 Git 环境：**

  - 每台**计算机上只需要配置一次**，程序升级时会保留配置信息
  - 你可以**在任何时候再次通过运行命令来修改它们**

- **Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量：**

  - **/etc/gitconfig 文件**：包含系统上每一个用户及他们仓库的通用配置

    如果在执行 git config 时带上 --system 选项，那么它就会读写该文件中的配置变量

    由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。（开发中通常不修改）

  - **~/.gitconfig 或 C/用户/XiaoYu/.gitconfig 文件**：只针对当前用户

    你可以传递 --global 选项让 Git 读写此文件，这会对你系统上 所有 的仓库生效

  - **当前使用仓库的 Git 目录中的 config 文件（即 .git/config）**：针对该仓库

    你可以传递 --local 选项让 Git 强制读写此文件，虽然默认情况下用的就是它

## (掌握)Git操作-Git初始化和clone远程仓库

### Git的配置选项

- **安装Git后，要做的第一件事就是设置你的用户名和邮件地址**
  - 这一点很重要，因为**每一个 Git 提交都会使用这些信息**，它们**会写入到你的每一次提交**中，不可更改
  - 如果使用了 **--global 选项，那么该命令只需要运行一次**，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息

```javascript
git config --global user.name "XiaoYu"
git user.emial "你的邮箱"
```

- **检测当前的配置信息：git config --list**

![image-20230220142928039](.\node_webpack_git_image\image-20230220142928039.png)

### Git的别名（alias）

- **Git 并不会在你输入部分命令时自动推断出你想要的命令：**
  - 如果不想每次都输入完整的 Git 命令，可以通过 git config 文件来轻松地为每一个命令设置一个别名

```javascript
//举例：
//它用于为 Git 设置一个全局别名（alias）来简化 checkout 命令的使用。具体来说，它会将 co 作为 checkout 命令的别名，这样你可以在命令行中使用 git co 代替 git checkout，从而更加快速和方便地切换 Git 分支
git config --global alias.co checkout
//使用这个命令的时候需要保证已经安装了 Git，并且在命令行中执行该命令。--global 标志表示该别名将被设置为全局别名，即在电脑的任何 Git 仓库中都可以使用
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

### 获取Git仓库 – git init/git clone

- **我们需要一个Git来管理源代码，那么我们本地也需要有一个Git仓库。**

- **通常有两种获取 Git 项目仓库的方式：**
  - 方式一：**初始化一个Git仓库**，并且可以将当前项目的文件都添加到Git仓库中（目前很多的脚手架在创建项目时都会默认创建一个Git仓库）
  - 方式二：**从其它服务器 克隆（clone） 一个已存在的 Git 仓库**（第一天到公司通常我们需要做这个操作）
- **方式一：初始化Git仓库**
  - 该命令将创建一个名为 .git 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的核心
  - 但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪

```javascript
git init//初始化仓库
Initialized empty Git repository in "地址"//git初始化好仓库返回提示

git add "文件"//这是将文件加入仓库最单纯的方式
git add .//你也可以这么做，相当于将所有文件都加进去，省得一个个加很麻烦
git log//查看我们提交了什么

e(exit)//使用git log查看完需要使用q才能退出查看效果
```

- **方式二：从Git远程仓库**

```javascript
//克隆下来别人在GitHub的仓库
git clone https://github.com/2002XiaoYu/Latest-front-end-Notes.git
```

## (掌握)Git操作-文件状态的划分

### 文件的状态划分

- **现在我们的电脑上已经有一个Git仓库：**
  - 在实际开发中，你需要将**某些文件交由这个Git仓库**来管理
  - 并且我们之后**会修改文件的内容**，当**达成某一个目标时，想要记录下来这次操作**，就**会将它提交到仓库**中
- **那么我们需要对文件来划分不同的状态，以确定这个文件是否已经归于Git仓库的管理：**
  - **未跟踪**：默认情况下，Git仓库下的文件也没有添加到Git仓库管理中，我们需要通过add命令来操作
  - **已跟踪**：添加到Git仓库管理的文件处于已跟踪状态，Git可以对其进行各种跟踪管理

- **已跟踪的文件又可以进行细分状态划分：**
  - **staged(暂缓)**：暂缓区中的文件状态，你`git log`是看不到的，但是他已经放到索引区里面了，只是没有跟commit对象联系起来(意思就是你还没跟任何的一次提交联系在一起)
  - **Unmodified**：commit命令，可以将staged中文件提交到Git仓库
  - **Modified**：修改了某个文件后，会处于Modified状态(就是处于已经修改的状态，但是还没丢到暂缓区去)，我们可以使用`git commit -m "添加某个文件和修改某个文件"`将这次修改也提交到暂缓区。等一大波操作结束可以将其重新放到git仓库
- **在工作时，你可以选择性地将这些修改过的文件放入暂存区**
- **然后提交所有已暂存的修改，如此反复。但是不管你是新增的文件还是修改的文件(包括已经在git仓库但进行的修改)都得先经过暂缓区**

![image-20230221020248589](.\node_webpack_git_image\image-20230221020248589.png)

### Git操作流程图

> | **Alice's Loacal Repo** | **对应本地仓库** |
> | ----------------------- | ---------------- |
> | **Remote Repo**         | **远程仓库**     |
> | **Bob's Local Repo**    | **本地仓库**     |
>
> | Working Directory  | 工作区                         |
> | ------------------ | ------------------------------ |
> | **Index**          | **对应着索引区，也就是staged** |
> | **.git Directory** | **git仓库(目录)**              |
>
> ```javascript
> git add.
> git commit -m ""
> //命令合二为一
> git commit -a -m "aaaa"//直接将工作区的内容提交到仓库(本地的，也交暂存区)
> 
> //-a 标志告诉 Git 自动将所有已修改和已删除的文件添加到暂存区中，这样你就不必使用 git add 命令显式地将更改添加到暂存区了。
> //-m "aaaa" 标志用于指定本次提交的提交信息。 "aaaa" 是提交信息的内容，它应该简洁地描述了这个提交所做的更改
> ```

![image-20230221021832652](.\node_webpack_git_image\image-20230221021832652.png)

## (掌握)Git操作-add-commit-status

### 检测文件的状态 - git status

- **我们在有Git仓库的目录下新建一个文件，查看文件的状态：**

```javascript
git status//查看状态命令(完整状态)
```

- **Untracked files：未跟踪的文件**
  - 未跟踪的文件意味着 Git 在之前的提交中没有这些文件
  - Git 不会自动将之纳入跟踪范围，除非你明明白白地告诉它“我需要跟踪该文件”
- **我们也可以查看更加简洁的状态信息：**

```javascript
//都是用来查看 Git 仓库当前状态的命令。它们的作用是一样的，都会以缩略的形式输出 Git 仓库中尚未提交的更改的概要信息。命令的输出是一样的，只是两者的命令参数略有不同。-s 是 --short 的简写形式
git status -s
git status --short

//两个字符的状态标识符，表示文件的状态。例如，M 表示文件已经修改，A 表示文件已经添加到暂存区，?? 表示文件未被跟踪等等。
//空格字符。
//文件名或路径。
//输出的状态标识符和文件名之间用空格分隔，每个文件占据一行。
```

```javascript
 M index.html//index.html 文件已经被修改但未添加到暂存区
 A images/logo.png//images/logo.png 文件已经添加到暂存区
?? README.md//README.md 文件是一个未被跟踪的新文件

//左栏指明了暂存区的状态，右栏指明了工作区的状态
```

### 文件添加到暂存区 – git add

- **跟踪新文件命令：**

  > 使用命令 git add 开始跟踪一个文件

```javascript
git add aaa.js
```

- **跟踪修改的文件命令：**
  - 如果我们已经跟踪了某一个文件，这个时候修改了文件也需要重新添加到暂存区中

跟踪文件：

![image-20230221070212659](.\node_webpack_git_image\image-20230221070212659.png)

修改了文件(需要先经过暂存区)：

![image-20230221070237553](.\node_webpack_git_image\image-20230221070237553.png)

- **通过git add . 将所有的文件添加到暂存区中：**

![image-20230221070250430](.\node_webpack_git_image\image-20230221070250430.png)

## (掌握)Git操作-Git忽略文件的配置方式

### git忽略文件

- **一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表**
  - 通常都是些**自动生成的文件**，比如日志文件，或者编译过程中创建的临时文件等
  - 我们可以创建一个名为 **.gitignore** 的文件，列出要忽略的文件的模式(`ignore`的英文意思为`忽略`)
  - 不想要git跟踪这些文件，就可以将这个文件的名字写进`.gitignore`里面，例如不想要他跟着xiaoyu.js文件，就直接写上xiaoyu.js然后独占一行就行了
- **在实际开发中，这个文件通常不需要手动创建，在必须的时候添加自己的忽略内容即可**
- **比如下面是创建的Vue项目自动创建的忽略文件：**
  - 包括一些不需要提交的文件、文件夹
  - 包括本地环境变量文件
  - 包括一些日志文件
  - 包括一些编辑器自动生成的文件

![image-20230221070431764](.\node_webpack_git_image\image-20230221070431764.png)

| .DS_Store                        | Mac电脑会自动生成的文件                                      |
| -------------------------------- | ------------------------------------------------------------ |
| **node_modules**                 | **包管理器，这个也不需要，我们只需要对应版本的package.json和lock.json文件就行了** |
| **/dist**                        | **打包后的文件直接部署了，不需要放仓库里(况且压缩过了也很难看懂改了啥)** |
| **.env,local和.evc.*.local**     | **本地文件，后面会写这些的作用**                             |
| **Log files底下的那些.log***     | **这些都是日志文件(工具生成的，用来记录一些bug之类的)**      |
| **Editor directories and files** | **编辑器自动生成的文件**                                     |

> Github提供的各个语言中需要忽略的文件项目(基本上不需要额外配置，脚手架也会默认配置)：
>
> - 地址：[github/gitignore: A collection of useful .gitignore templates](https://github.com/github/gitignore)

## (掌握)Git操作-Git校验和-日志和版本回退

### 文件更新提交 – git commit

- **现在的暂存区已经准备就绪，可以提交了**

  - 每次准备提交前，先用 git status 看下，你所需要的文件是不是都已暂存起来了
  - 再运行提交命令 git commit
  - 可以在 commit 命令后添加 -m 选项，将提交信息与命令放在同一行

  ```javascript
  git commit -m "提交信息"//刚刚有讲到
  ```

  ![image-20230221072932275](.\node_webpack_git_image\image-20230221072932275.png)

- **如果我们修改文件的add操作，加上commit的操作有点繁琐，那么可以将两个命令结合来使用：**

  ```javascript
  git commit -a -m "修改了bbb文件"
  ```

  ![image-20230221072958338](.\node_webpack_git_image\image-20230221072958338.png)

### Git的校验和

- **Git 中所有的数据在存储前都计算校验和，然后以 校验和 来引用**
  - Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）
  - 这是一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，基于 Git 中文件的内容或目录结构计算出来

> A B C分别是3次提交，我们每一次提交都是一次快照，我们可以通过校验和找到tree，在通过tree找到所有的文件，所谓的版本回退一样是找以前的校验和，然后找到对应的索引tree，在通过tree找到对应的文件，然后再将这些文件恢复到我们目前提交的状态

![image-20230221073024658](.\node_webpack_git_image\image-20230221073024658.png)

![image-20230221073330761](.\node_webpack_git_image\image-20230221073330761.png)

### 查看提交的历史 – git log

- **在提交了若干更新，又或者克隆了某个项目之后，有时候我们想要查看一下所有的历史提交记录**
- **这个时候我们可以使用git log命令：**
  - 不传入任何参数的默认情况下，git log 会按时间先后顺序列出所有的提交，最近的更新排在最上面
  - 这个命令会列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明

```js
git log//提交过多可以按空格继续往下看内容
```

![image-20230221073920244](.\node_webpack_git_image\image-20230221073920244.png)

```js
git log --pretty=oneline --graph(图结构)//显示 Git 仓库的提交历史，它会将提交历史以 ASCII 码图的形式展示出来

//显示提交历史：该命令会显示 Git 仓库的提交历史，包括每次提交的 SHA-1 校验和、提交信息和提交日期等信息。
//显示分支结构：--graph 选项会在 ASCII 码图中显示分支结构，使得更容易看出不同分支之间的关系和合并情况。
//美化输出格式：--pretty=oneline 选项会将每个提交压缩成一行，使得输出更加紧凑和易于阅读。

//可以更清晰地了解 Git 仓库的提交历史，从而更好地管理代码版本，特别是在多人协作的项目中
```

![image-20230221074731176](.\node_webpack_git_image\image-20230221074731176.png)

> 上面的图是一开始所处的状态(不加--graph)，你会发现下面的图左边还有图形化的分支，这是加上--graph 选项的显示方式，用`*`做出了区分

![image-20230221073929680](.\node_webpack_git_image\image-20230221073929680.png)

```js
git log --pretty=oneline//不加graph，输出前面没有*
```

![image-20230221073940384](.\node_webpack_git_image\image-20230221073940384.png)

### 版本回退 – git reset

- **如果想要进行版本回退，我们需要先知道目前处于哪一个版本：Git通过HEAD指针记录当前版本**
  - HEAD 是当前分支引用的指针，它总是指向该分支上的最后一次提交
  - 理解 HEAD 的最简方式，就是将它看做 **该分支上的最后一次提交** 的快照
  - reset = 重置

![image-20230221074841776](.\node_webpack_git_image\image-20230221074841776.png)

- **我们可以通过HEAD来改变Git目前的版本指向：**
  - 上一个版本就是HEAD^，上上一个版本就是HEAD^^
  - 如果是上1000个版本，我们可以使用HEAD~1000
  - 我们可以可以指定某一个commit id

```javascript
git reset --hard HEAD^
//这个命令会将当前分支的 HEAD 指针重置到上一次提交（也就是父提交），并将工作区和暂存区的内容也回退到上一次提交时的状态。这相当于撤销了最近一次的提交，并把代码回滚到了上一次提交的状态
git reset --hard HEAD~1000
//这个命令会将当前分支的 HEAD 指针重置到前1000次提交的位置，并将工作区和暂存区的内容也回退到该提交时的状态。这相当于回滚到前1000次提交的状态，慎用
git reset --hard 2d44982
//这个命令会将当前分支的 HEAD 指针重置到指定的提交 2d44982，并将工作区和暂存区的内容也回退到该提交时的状态。这相当于回滚到指定提交的状态

//git reset --hard 命令是一种危险的操作，因为它会删除当前分支指向的提交之后的所有提交，如果你不小心操作可能会丢失你的代码修改。所以在使用这个命令时，要非常小心并且确保备份了重要的代码修改
```

> 移动HEAD就进行切换版本

![image-20230221075028430](.\node_webpack_git_image\image-20230221075028430.png)

```javascript
git reflog//记录下来我们每一次的操作
```

## (理解)Git远程仓库-常见的Git服务器和远程仓库

### 什么是远程仓库？

- **什么是远程仓库（Remote Repository）呢？**
  - 目前我们的代码是保存在一个本地仓库中，也就意味着我们只是在进行本地操作
  - 在真实开发中，我们通常是多人开发的，所以我们会将管理的代码共享到远程仓库中
- **那么如何创建一个远程仓库呢？**
  - 远程仓库通常是搭建在某一个服务器上的（当然本地也可以，但是本地很难共享）
  - 所以我们需要在Git服务器上搭建一个远程仓库
- **目前我们有如下方式可以使用Git服务器：**
  - 使用第三方的Git服务器：比如GitHub、Gitee、Gitlab等等
  - 在自己服务器搭建一个Git服务(使用Gitlab软件，跟上面的Gitlab是不一样的)

![image-20230221080138595](.\node_webpack_git_image\image-20230221080138595.png)

### 远程仓库的验证

- **常见的远程仓库有哪些呢？目前比较流行使用的是三种：**
  - GitHub：https://github.com/
  - Gitee(国内用的较多，现在这个创建开源项目很麻烦)：https://gitee.com/
  - 自己搭建Gitlab：http://152.136.185.210:7888/
- **对于私有的仓库我们想要进行操作，远程仓库会对我们的身份进行验证：**
  - 如果没有验证，任何人都可以随意操作仓库是一件非常危险的事情
- **目前Git服务器验证手段主要有两种：**
  - 方式一：基于**HTTP的凭证存储**（Credential Storage）
  - 方式二：基于**SSH的密钥**
- **具体讨论一下这两种方式的验证规则和过程**

## (掌握)Git远程仓库-远程仓库的创建和验证方式

> 设置模板里面我们通常会选择Readme.md(自述文件)，其他两种通常是开源会选的

![image-20230221082301942](.\node_webpack_git_image\image-20230221082301942.png)

> ```javascript
> git clone "https://gitee.com/XiaoYu-2002/Latest-front-end-Notes.git"//HTTP地址私人仓库需要验证
> git clone "git@gitee.com:XiaoYu-2002/Latest-front-end-Notes.git"//SSH
> //通常进公司第一件事情就是克隆下来代码
> ```

![image-20230221082730267](.\node_webpack_git_image\image-20230221082730267.png)

## (掌握)Git远程仓库-验证方式-凭证

### 远程仓库的验证 – 凭证

- **因为本身HTTP协议是无状态的连接，所以每一个连接都需要用户名和密码：**
  - 如果每次都这样操作，那么会非常麻烦
  - 幸运的是，Git 拥有一个凭证系统(账号密码其实也算一种凭证)来处理这个事情

- **下面有一些 Git Crediential 的选项：**

  - 选项一：默认所有都不缓存。 每一次连接都会询问你的用户名和密码

  - 选项二：“cache” 模式会将凭证存放在内存中一段时间。 密码永远不会被存储在磁盘中，并且在15分钟后从内存中清除

  - 选项三：“store” 模式会将凭证用明文的形式存放在磁盘中，并且永不过期(不安全)

  - 选项四：如果你使用的是 Mac，Git 还有一种 “osxkeychain” 模式，它会将凭证缓存到你系统用户的钥匙串中（加密的）

  - 选项五：如果你使用的是 Windows，你可以安装一个叫做 “Git Credential Manager for Windows” 的辅助工具

    可以在 https://github.com/Microsoft/Git-Credential-Manager-for-Windows 下载

```javascript
git config credential.helper//检查我们电脑是否有Git Credential Manager for Windows这个工具

//如果 git config credential.helper 命令返回的结果是 "manager"，这可能意味着你之前设置了 Git 凭据管理器作为凭据帮助程序。"manager" 实际上是 wincred 凭据帮助程序的别名，它会将 Git 凭据存储在 Windows 凭据管理器中
```

![image-20230221094626356](.\node_webpack_git_image\image-20230221094626356.png)

> `git config credential.helper` 是 Git 的一个配置命令，它的作用是配置 Git 的认证帮助程序（credential helper），用于处理 Git 账户的凭据管理和认证。
>
> Git 用于和远程 Git 仓库交互的协议通常需要进行身份认证，比如使用 HTTPS 或 SSH 连接到 Git 服务器。在使用 Git 进行 push、pull 等操作时，用户需要提供用户名和密码等凭据来进行身份验证，而 `credential.helper` 可以帮助我们管理这些凭据。
>
> 通过设置 `credential.helper`，Git 将会调用一个外部的认证帮助程序来管理凭据，从而实现自动化身份认证。常见的凭据帮助程序有：
>
> - cache：将凭据缓存到内存中一段时间，减少重复输入密码的次数。
> - store：将凭据保存在明文文件中，不建议在安全性较高的环境中使用。
> - wincred：将凭据保存在 Windows 凭据管理器中，可以避免密码泄露。
>
> 例如，通过执行以下命令可以将 `credential.helper` 设置为使用 Windows 凭据管理器：
>
> ```js
> git config --global credential.helper wincred
> ```
>
> 这样，Git 就会将凭据存储在 Windows 凭据管理器中，避免了密码泄露的风险，并且可以避免频繁地输入密码。

## (掌握)Git远程仓库-验证方式-SSH密钥

### 远程仓库的验证 – SSH密钥

- **Secure Shell（安全外壳协议，简称SSH）是一种加密的网络传输协议，可在不安全的网络中为网络服务提供安全的传输环境**

- **SSH以非对称加密实现身份验证**

  - 例如其中一种方法是使用自动生成的公钥-私钥对来简单地加密网络连接，随后使用密码认证进行登录
  - 另一种方法是人工生成一对公钥和私钥，通过生成的密钥进行认证，这样就可以在不输入密码的情况下登录
  - 公钥需要放在待访问的电脑之中，而对应的私钥需要由用户自行保管

  > 意思就是电脑生成一对公私钥，私钥自己保存，公钥交出去给服务器。然后等要访问服务器的时候，会将电脑上的私钥自动去匹配服务器上的公钥。这串公私钥是非对称加密的

- **如果我们以SSH的方式访问Git仓库，那么就需要生产对应的公钥和私钥：**

```js
ssh-keygen -t ed25519 -C "XiaoYu20020805@gmail.com"//现在更多使用这种方式
//ssh-keygen -t ed25519 -C "your email" 是用于生成 ed25519 算法的 SSH 密钥。ed25519 是一种非对称加密算法，可以用于在计算机之间建立安全连接。该命令将生成一个 ed25519 密钥对，其中包括一个私钥和一个公钥。私钥将保存在你的本地计算机上，而公钥可以在需要访问的服务器上安装，方便你可以通过 SSH 连接到该服务器

ssh-keygen -t rsa -b 2048 -C "XiaoYu20020805@gmail.com"
//sh-keygen -t rsa -b 2048 -C "your email" 是用于生成 RSA 算法的 SSH 密钥。RSA 是一种非对称加密算法，也可用于在计算机之间建立安全连接。该命令将生成一个 RSA 密钥对，其中包括一个私钥和一个公钥。私钥将保存在你的本地计算机上，而公钥可以在需要访问的服务器上安装，以便你可以通过 SSH 连接到该服务器

//这两个命令中的 -C 参数是用于添加注释，以标识密钥的拥有者。"XiaoYu20020805@gmail.com" 应替换为你的电子邮件地址(一般也填这个)
```

![image-20230221100729834](.\node_webpack_git_image\image-20230221100729834.png)

> 有上面框框里面的那些就是我们的公私钥已经生成好了，在资源管理器的如下地址中可以找到我们的公私钥

![image-20230221101052910](.\node_webpack_git_image\image-20230221101052910.png)

> 打开PUB这个文件，拿到里面的公钥内容。然后去我们的平台设置公钥

![image-20230221101422140](.\node_webpack_git_image\image-20230221101422140.png)

> 输入密码即可验证成功

![image-20230221101455809](.\node_webpack_git_image\image-20230221101455809.png)

![image-20230221101506331](.\node_webpack_git_image\image-20230221101506331.png)

- 这样我们就能够直接使用SSH的方式进行克隆私有仓库的代码`git clone "SSH地址"`
- 写完内容，直接`git push`就能更新服务器中的内容了

## (掌握)Git远程仓库-远程仓库的添加和同步

### 管理远程服务器

- **查看远程地址：比如我们之前从GitHub上clone下来的代码，它就是有自己的远程仓库的：**

```js
git remote
git remote –v
-v是—verbose的缩写(冗长的)
```

![image-20230221101949756](.\node_webpack_git_image\image-20230221101949756.png)

- **添加远程地址：我们也可以继续添加远程服务器（让本地的仓库和远程服务器仓库建立连接）：**

  > 这个 Git 命令用于将一个远程 Git 仓库与本地 Git 仓库建立连接，让你可以从本地 Git 仓库向远程 Git 仓库推送代码或者从远程 Git 仓库拉取代码到本地 Git 仓库

```javascript
git remote add <shortname> <url>
//<shortname> 是给远程 Git 仓库指定的一个简短的别名，方便在以后的 Git 命令中使用，比如通常用 "origin" 来作为默认的远程仓库的别名。
//<url> 是远程 Git 仓库的 URL 地址，可以是 HTTPS 或 SSH 协议的地址。
  
git remote add gitlab http://152.136.185.210:7888/coderwhy/gitremotedemo.git
//这样就可以在以后的 Git 命令中使用 "gitlab" 这个别名来代替远程 Git 仓库的 URL 地址了
```

![image-20230221102018326](.\node_webpack_git_image\image-20230221102018326.png)

- **重命名远程地址：**

```
git remote rename gitlab glab
```

- **移除远程地址：**

```js
git remote remove gitlab
```

### 远程仓库的交互

- **从远程仓库clone代码：将存储库克隆到新创建的目录中；**

```javascript
git clone "地址"
```

- **将代码push到远程仓库：将本地仓库的代码推送到远程仓库中；**
  - 默认情况下是将当前分支（比如master）push到origin远程仓库的

```javascript
git push
//这个命令将本地代码库中的修改上传到远程代码库。如果你之前使用过 git clone 或者 git remote add 命令，那么在执行 git push 时就会将本地代码库中的内容推送到默认的远程代码库中
git push origin master
//这个命令将本地代码库中的修改上传到名为 origin 的远程代码库中的 master 分支。如果你有多个远程代码库，那么可以使用 git remote 命令来查看它们的名称，并将 origin 替换为你想要推送到的远程代码库的名称

//在执行这些命令之前，你需要先使用 git add 和 git commit 命令将本地的修改保存到本地代码库中。这样才能通过 git push 命令将这些修改推送到远程代码库中
```

- **从远程仓库fetch代码：从远程仓库获取最新的代码**
  -  默认情况下是从origin中获取代码

```javascript
git fetch
git fetch origin
```

- 获取到代码后默认并没有合并到本地仓库，我们需要通过merge来合并

```javascript
git merge//merge中文意思：合并
```

- **从远程仓库pull代码：上面的两次操作有点繁琐，我们可以通过一个命令来操作**(代码合并)

```javascript
git fetch//将代码从服务器拿下来(放在仓库里，而不是我们编写的代码里)
git merge//跟我们当前编写的代码做出合并

git pull = git fetch + git merge//合二为一(拉下来合并)

git push//提交代码(将代码推到仓库)
//以上4个就是平时最常用的命令
```

## (理解)Git远程仓库-fetch和merge遇到的问题处理

> 我们`git init`初始化一个本地仓库，这个时候我们的本地文件内容还没有放进去本地仓库里面(就是没有进行跟踪)，文件的右边有个`U`意思是untracked(未跟踪)

![image-20230221182702386](.\node_webpack_git_image\image-20230221182702386.png)

> 第一步：`git add.`将其进行添加进去本地仓库(跟踪)，这个时候状态变成了A

![image-20230221182836145](.\node_webpack_git_image\image-20230221182836145.png)

> 第二步：第一次提交`git commit -m "初始化项目"`
>
> 这个时候，我们当前的本地仓库还没有跟远程仓库建成连接
>
> 第三步：使用命令`git remote add "给本地仓库起名，通常origin" "远程仓库连接"`建立起本地仓库和远程仓库的连接
>
> 使用git remote -v就可以看到我们所连接的远程仓库都有哪些了
>
> 第四步：这里如果使用`git pull`就会报错，目前知道我们的分支是`master`(GitHub默认分支是main)，但是真正开发的时候还会有其他的分支(本地仓库有多个分支，远程仓库也可以多个分支)
>
> 那我们就可以开始解决报错问题了：已知我们本地的分支是master，注意第三步中我们只是建立起本地仓库跟远程仓库的连接，但是没有说跟远程仓库的哪个分支进行连接

```javascript
//指定拉取(pull)远程仓库某个分支的内容
git push origin master
//这个命令将本地代码库中的修改上传到名为 origin 的远程代码库中的 master 分支。如果你有多个远程代码库，那么可以使用 git remote 命令来查看它们的名称，并将 origin 替换为你想要推送到的远程代码库的名称
```

> 注意：
>
> 在 Git 2.28 及之前的版本中，新创建的本地仓库默认分支名为 `master`。但是自 Git 2.28 版本开始，Git 的默认分支名已经被更改为 `main`，这是为了避免与潜在的不包容性语言相关的术语有关(master有主人的意思，为了避免种族歧视，所以后续就修改掉了)。
>
> 因此，如果你的 Git 版本在 2.28 及之前的版本，那么在初始化新的本地仓库时，默认分支名将是 `master`。如果你使用的是 Git 2.28 或更新版本，则默认分支名将是 `main`。不过，在初始化仓库时，你可以指定任何分支名作为默认分支。例如，使用以下命令可以在初始化时指定分支名为 `XiaoYu`：

```js
git init --initial-branch=XiaoYu//branch的中文意思：分支
```

> 指定上游分支(什么是上游分支，我们在接下来会进行讲解)

```javascript
git branch --set-upstream-to=origin/master
//返回branch 'master' set up to track 'origin/master'，也就是'master'分支已经去跟踪'origin'的master分支了
git pull//此时直接执行pull命令就不会报错了
```

### 本地分支的上游分支（跟踪分支）

- 问题一：当前分支没有track的分支

![image-20230221190935705](.\node_webpack_git_image\image-20230221190935705.png)

- 原因：当前分支**没有和远程的origin/master分支进行跟踪**
  - 在没有跟踪的情况下，我们直接执行**pull操作的时候必须指定**从哪一个远程仓库中的哪一个分支中获取内容

![image-20230221190952065](.\node_webpack_git_image\image-20230221190952065.png)

- 如果我们想要直接执行git fetch是有一个前提的：必须给当**前分支设置一个跟踪分支**：

![image-20230221191002054](.\node_webpack_git_image\image-20230221191002054.png)![image-20230221191006666](.\node_webpack_git_image\image-20230221191006666.png)

> 在 Git 中，"upstream branch" 指的是与当前本地分支所跟踪的远程分支相关联的远程分支。换句话说，它是一个与当前本地分支相关联的远程分支，它被用来同步远程分支和本地分支之间的代码更改。
>
> 当你克隆一个 Git 仓库时，通常会自动创建一个默认的本地分支，它会跟踪克隆时指定的远程分支，这个远程分支就是这个本地分支的上游分支。
>
> 如果你的本地分支没有跟踪任何远程分支，那么它就没有上游分支。在这种情况下，你需要手动设置远程分支作为本地分支的上游分支。可以使用以下命令来设置本地分支的上游分支：
>
> ```
> git branch --set-upstream-to=<remote-branch>
> ```
>
> 这个命令会将当前所在分支的上游分支设置为指定的远程分支。
>
> 有了上游分支，你可以使用 Git 命令比如 `git fetch` 和 `git pull` 来从远程仓库获取最新的代码，并将其合并到本地分支。同时，你也可以使用 `git push` 将本地分支的更改推送到上游分支。
>
> 上游分支是 Git 中一个非常重要的概念，它帮助我们更好地管理本地分支和远程分支之间的同步

### 拒绝合并不相干的历史

- 问题二：合并远程分支时，拒绝合并不相干的历史

![image-20230221191359953](.\node_webpack_git_image\image-20230221191359953.png)

- 原因：我们将两个不相干的分支进行了合并：
  - https://stackoverflow.com/questions/37937984/git-refusing-to-merge-unrelated-histories-on-rebase

![image-20230221191433961](.\node_webpack_git_image\image-20230221191433961.png)

- 简单来说就是：过去git merge允许将两个没有共同基础的分支进行合并，这导致了一个后果：新创建的项目可能被一个毫不怀疑的维护者合并了很多没有必要的历史到一个已经存在的项目中，目前这个命令已经被纠正，但是我们依然可以通过`--allow-unrelated-histories`选项来逃逸这个限制，来合并两个独立的项目

![image-20230221191540205](.\node_webpack_git_image\image-20230221191540205.png)

## (理解)Git远程仓库-远程仓库总结和公司开发流程

- 情况1：你到公司之后公司有项目了，并且有远程仓库了
  1. git clone "xxx"，先把公司的代码拉下来
  2. 进行开发：
     - git add(跟踪本地仓库)
     - git commit "提交" 
     - git push(提交代码，在这之前最好先git pull一下，将最新代码拉去下来合并，不然同事可能已经传递一些东西上去了)
- 情况2：开发一个全新的项目(由你来搭建)
  - 方案1：创建远程仓库
    - 首先需要有个远程仓库 => 创建一个远程仓库
    - `git clone`克隆下来，在克隆下来的文件夹中搭建整个项目
    - `git add .`对项目进行跟踪。`git commit -m`，将项目提交到暂存区
    - `git push`将代码提交到远程仓库
  - 方案2：创建一个本地仓库和搭建本地项目
    - `git remote add origin "项目地址"`将本地仓库跟远程仓库建立连接
    - `git branch --set-upstream-to=origin/master`将本地仓库跟远程仓库的某个分支建立连接
    - `git fetch`拉下全新的代码，然后`git merge --allow-unrelated-histories`合并代码
    - `git push`提交代码

## (掌握)GitHub-GitHub的作用和查找开源项目

> GitHub：全球最优秀也是最强大的程序员交流平台，开源平台
>
> 需要一点点手段才能够正常使用这个网站(手段教不了)，要么就换个时间段登录(打不开的情况)。注册账号的话自己CSDN看看怎么注册的
>
> 搜索对应的源码通过`git clone`克隆下来学习

### git服务器

| 创建的仓库状态 | 作用                             | issue(问题)                          |
| -------------- | -------------------------------- | ------------------------------------ |
| public(公开)   | 开源，所有人都能看到你的源代码   | 有人看到你代码里有问题就会给你提问题 |
| private(私有)  | 只有自己或者自己所属的团队能看到 | 闭源私有的，没有这功能               |

## (理解)GitHub-创建开源项目和开源协议

![image-20230221214925553](.\node_webpack_git_image\image-20230221214925553.png)

> 开源协议的不同选择，决定了别人使用我们这个仓库的程度最多能到达什么程度

### 常见的开源协议

![image-20230221215124415](.\node_webpack_git_image\image-20230221215124415.png)

## (掌握)GitHub-Gitlab的常见操作演练

Gitlab的创建仓库界面

![image-20230221221801997](.\node_webpack_git_image\image-20230221221801997.png)

GitHub的SSH公钥上传跟gitee差不多

![image-20230221222826255](.\node_webpack_git_image\image-20230221222826255.png)

> 大量实际操作需要自己看一下

### Git标签（tag） - 删除和检出tag

- **删除本地tag：**

  - 要删除掉你本地仓库上的标签，可以使用命令 **git tag -d <tagname>**

  ![image-20230221223910749](.\node_webpack_git_image\image-20230221223910749.png)

- **删除远程tag：**

  - 要删除远程的tag我们可以通过**git push <remote> –delete <tagname>**

  ![image-20230221223931107](.\node_webpack_git_image\image-20230221223931107.png)

- **检出tag：**

  - 如果你想查看某个标签所指向的文件版本，可以使用 **git checkout** 命令
  - 通常我们在检出tag的时候还会创建一个对应的分支（分支后续了解）

  ![image-20230221223954299](.\node_webpack_git_image\image-20230221223954299.png)

## (理解)GitHub-push操作的默认行为

```javascript
git push origin master//用于将本地代码仓库的 master 分支推送（push）到远程代码仓库的 origin 主机上
//git push 命令用于将本地代码仓库中的代码上传到远程代码仓库，而 origin 表示远程代码仓库的主机名，而 master 表示本地代码仓库中的主分支。因此，git push origin master 的意思是将本地代码仓库中的 master 分支推送到远程代码仓库的 origin 主机上
----------------------------------------
git push origin master:main
//意思是将本地代码仓库中的 master 分支推送到远程代码仓库的 main 分支上。这个命令会将本地的 master 分支的代码推送到远程代码仓库的 main 分支，并覆盖远程分支上的所有内容。通常，这个命令用于在本地的 master 分支上进行开发，然后将更改推送到远程仓库的 main 分支上

git push origin head:main//head默认情况下就是指向master的
//将当前分支的代码推送到远程代码仓库的 main 分支上。这个命令会将本地当前分支的代码推送到远程代码仓库的 main 分支，并覆盖远程分支上的所有内容。与上一个命令不同的是，这个命令可以用于推送任何本地分支的更改，而不仅仅是 master 分支

//在使用这些命令之前，我们需要确保本地代码仓库和远程代码仓库进行了正确的关联，并且有权限将代码推送到远程仓库。另外，如果远程代码仓库中不存在指定的远程分支，则 Git 会自动创建一个新的分支并将代码推送到该分支上
```

- git push的默认设置行为：
  - `git config push.default simple`，什么都不写的情况下默认是simple
  - `git config push.default.upstream`，修改为upstream
  - `git config push.default.current`，修改为current

> - 如果设置为 `upstream`（或者是其同义词 `tracking`），则表示 `git push` 命令将只推送当前分支所跟踪的远程分支（也就是 upstream 分支）。例如，如果当前本地分支是 `feature`，并且它跟踪的远程分支是 `origin/feature`，那么运行 `git push` 命令时，Git 将只会推送本地 `feature` 分支的更改到 `origin/feature` 分支上。
>
> - 如果设置为 `simple`，则表示 `git push` 命令将只推送当前分支所跟踪的远程分支，但是会限制推送操作的条件，即只有在当前分支的 upstream 分支是远程代码库中存在的分支，并且本地分支与 upstream 分支同名时，才能成功执行推送。这样可以避免误推送到错误的分支。
>
> - 如果设置为 `matching`，则表示 `git push` 命令将推送所有与本地分支同名的远程分支。例如，如果当前本地分支是 `feature`，那么运行 `git push` 命令时，Git 将会尝试推送所有名为 `feature` 的远程分支。
>
> - `git config push.default.current` 是 Git 中的一个配置选项，它指定了在不带任何参数的情况下使用 `git push` 命令时应该如何处理推送操作。具体来说，这个配置项的含义如下：
>
>   - 如果设置为 `current`，则表示 `git push` 命令将只推送当前分支到远程仓库中同名的分支。例如，如果当前本地分支是 `feature`，那么运行 `git push` 命令时，Git 将只会推送本地 `feature` 分支的更改到远程仓库的 `feature` 分支上。
>
>   这种配置相对于其他选项来说比较简单明了，但需要注意的是，这种配置有一定的危险性，因为如果你误操作了，可能会不小心把本地分支的修改推送到了错误的分支上。因此，建议在使用 `git push` 命令时要慎重考虑是否使用该选项，并根据实际情况选择合适的推送策略。
>
> 需要注意的是，从 Git 2.0 版本开始，`push.default` 配置项的默认值被改为 `simple`。因此，如果没有显式地设置该配置项，那么默认情况下 `git push` 命令将使用 `simple` 模式进行推送操作

```js
cat .git/config
//作用：用于查看当前 Git 仓库的配置信息。在 Git 中，每个仓库都有一个 .git 目录，其中包含了该仓库的所有信息，包括对象库、配置文件等

//在 Git 中，配置信息被存储在 .git/config 文件中，这个文件是每个仓库都独立存在的。使用 cat .git/config 命令可以打印出这个文件的内容，从而查看该仓库的配置信息，包括如下内容：

//仓库的名称和描述
//远程仓库的信息，如 URL 和分支映射
//分支的信息，如跟踪的远程分支、合并方式等
//用户的身份信息，如用户名、邮箱等
//Git 的行为配置，如行为、合并策略等
//通过查看 .git/config 文件，你可以了解该仓库的配置信息，包括远程仓库的地址、分支信息、用户名等，以及 Git 的行为配置，这对于理解 Git 的行为和解决一些问题是很有帮助的
```

## (掌握)git-tag-tag的使用过程

### Git标签（tag） - 创建tag

- **对于重大的版本我们常常会打上一个标签，以表示它的重要性：**

  - Git 可以给仓库历史中的某一个提交打上标签
  - 比较有代表性的是人们会使用这个功能来标记发布结点（ v1.0 、 v2.0 等等）
  - 这样方便进行版本回退

- **创建标签：**

  - Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）
  -  附注标签：通过-a选项，并且通过-m添加额外信息

  ![image-20230222105419384](.\node_webpack_git_image\image-20230222105419384.png)![image-20230222105423870](.\node_webpack_git_image\image-20230222105423870.png)

- **默认情况下，git push 命令并不会传送标签到远程仓库服务器上**

  - 在创建完标签后你必须显式地推送标签到共享服务器上，当其他人从仓库中克隆或拉取，他们也能得到你的那些标签

  ```js
  git push 远程仓库别名 推送的版本
  ```

  ![image-20230222105444849](.\node_webpack_git_image\image-20230222105444849.png)![image-20230222105451085](.\node_webpack_git_image\image-20230222105451085.png)

### Git标签（tag） - 删除和检出tag

- **删除本地tag：**

  - 要删除掉你本地仓库上的标签，可以使用命令 `git tag -d <tagname>`

    ![image-20230222104504906](.\node_webpack_git_image\image-20230222104504906.png)

- **删除远程tag：**

  - 要删除远程的tag我们可以通过`git push <remote> –delete <tagname>`
  - --delete可以简写为-d

  ![image-20230222104549300](.\node_webpack_git_image\image-20230222104549300.png)

- **检出tag：**(查看当前所有的tag)

  - 如果你想查看某个标签所指向的文件版本，可以使用 **git checkout** 命令
  - 通常我们在检出tag的时候还会创建一个对应的分支（分支后续了解）

  ![image-20230222104631554](.\node_webpack_git_image\image-20230222104631554.png)

## (理解)git的提交对象和底层原理

> 当你git init初始化一个新的仓库的时候，还没有进行跟踪，只有如下两个文件

![image-20230222122011739](.\node_webpack_git_image\image-20230222122011739.png)

> 当你进行`git add.`进行全部跟踪之后，多出来的两个文件夹。两个文件夹里面是二进制文件

![image-20230222122109871](.\node_webpack_git_image\image-20230222122109871.png)

> `git cat-file -t 00xx`，其中xx是指00文件夹里面的二进制文件名字前面几个字母数字内容，通常就写四五个，达到没有跟当前文件夹其他的二进制文件重复就行。
>
> - `git cat-file` 命令是一个多功能命令，可以用来查看 Git 对象的内容、类型和大小等信息。其中 `-t` 选项表示查看对象的类型（type），而 `00xx` 则是要查看的对象的 SHA-1 值
> - 通过查看对象类型，可以帮助我们更好地了解 Git 中的对象模型和数据结构，以及更好地理解 Git 内部是如何存储和管理版本控制数据的
>
> `git cat-file -p 00xx`，跟上面的区别是一个-p，另一个-t。那他们的区别是什么？
>
> - `-p` 选项表示打印对象的内容（payload），也就是打印出该对象存储的实际数据。这通常是一段文本，它的格式取决于对象的类型。例如，如果指定的对象是一个提交对象（commit object），那么执行 `git cat-file -p 00xx` 命令会输出该提交对象的元数据（如作者、提交日期等）以及提交消息。如果指定的对象是一个文件对象（blob object），那么执行相同的命令会输出该文件的内容。
> - `-t` 和 `-p` 选项在作用上是不同的，前者用于查看对象的类型，而后者用于查看对象的实际内容。两个选项的选择取决于你想要查看的信息类型Git提交对象（Commit Object）

- 当你在跟踪的基础上使用`git commit -m "初始化项目"`提交一次代码到暂缓区后，又会多出来两个文件，如果我们对这多出来的两个文件用刚刚的`git cat-file -p xxxx`来查看的话

![image-20230222194643081](.\node_webpack_git_image\image-20230222194643081.png)

> 我们查看到的内容是上一个跟踪的文件索引还有类型以及对应的文件。那多出来的另外一个文件放的是什么？是我们真正的提交文件

![image-20230222194826332](.\node_webpack_git_image\image-20230222194826332.png)

> 让我们进行观察一下就能发现规律

![image-20230222195053329](.\node_webpack_git_image\image-20230222195053329.png)



- **几乎所有的版本控制系统都以某种形式支持分支。**

  - 使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线

- **在进行提交操作时，Git 会保存一个提交对象（commit object）：**

  - 该提交对象会包含一个指向暂存内容快照的指针；
  - 该提交对象还包含了作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针

  首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象

  而由多个分支合并产生的提交对象有多个父对象

![image-20230221224104518](.\node_webpack_git_image\image-20230221224104518.png)

![image-20230221224108059](.\node_webpack_git_image\image-20230221224108059.png)

## (掌握)git分支-创建和切换多个分支和本质

### Git master分支

- **Git 的分支，其实本质上仅仅是指向提交对象的可变指针。**
  - Git 的默认分支名字是 master，在多次提交操作之后，你其实已经有一个指向最后那个提交对象的 master 分支
  - master 分支会在每次提交时自动移动
- **Git 的 master 分支并不是一个特殊分支**
  - 它就跟其它分支完全没有区别
  - 之所以几乎每一个仓库都有 master 分支，是因为 git init 命令默认创建它(现在也可能叫main)，并且大多数人都懒得去改动它

![image-20230221224154174](.\node_webpack_git_image\image-20230221224154174.png)

### Git创建分支

- **Git 是怎么创建新分支的呢？**
  - 很简单，它只是为你创建了一个可以移动的新的指针

![image-20230221224228945](.\node_webpack_git_image\image-20230221224228945.png)

- **比如，创建一个 testing 分支， 你需要使用 git branch 命令：**

```js
git branch testing//创建名为testing的新分支，但是当前分支没有切换过去
```

- **那么，Git 又是怎么知道当前在哪一个分支上呢？**
  -  也很简单，它也是通过一个名为 HEAD 的特殊指针

![image-20230221224301216](.\node_webpack_git_image\image-20230221224301216.png)![image-20230221224303341](.\node_webpack_git_image\image-20230221224303341.png)

### Git分支提交

- **如果我们指向某一个分支，并且在这个分支上提交：**

![image-20230221224327418](.\node_webpack_git_image\image-20230221224327418.png)

- **你也可以切换回到master分支，继续开发：**

![image-20230221224341996](.\node_webpack_git_image\image-20230221224341996.png)

![image-20230221224345192](.\node_webpack_git_image\image-20230221224345192.png)

### 创建分支同时切换

- **创建新分支的同时切换过去**
  - 通常我们会在创建一个**新分支后立即切换过去**
  - 这可以用 **git checkout -b <newbranchname>** 一条命令搞定
  - `git branch`查看当前所有分支

```js
git checkout -b <newbranchname>
//本地分支跟远程仓库分支的名字一样的话，就不需要写本地跟远程两个仓库分支的名字，而是只需要写一个当成两个用就行了
  
//git checkout 命令用于切换分支或恢复工作区文件，这里用于切换分支。
//-b 参数表示要创建新分支。
//<newbranchname> 是新分支的名称。
//因此，运行 git checkout -b <newbranchname> 命令将会创建一个新的名为 <newbranchname> 的分支，并切换到该分支。如果当前分支上有未提交的更改，Git 会提示你先提交或保存这些更改，然后再创建新分支
  
//不创建新分支的话，只想单纯切换到已有分支也可以git checkout xxx，xxx是你想切过去的分支
```

## (掌握)Git分支-实际工作中分支的使用演练

### 为什么需要使用分支呢？

- **让我们来看一个简单的分支新建与分支合并的例子，实际工作中你可能会用到类似的工作流**
  - 开发某个项目，在默认分支master上进行开发
  - 实现项目的功能需求，不断提交
  - 并且在一个大的版本完成时，发布版本，打上一个tag v1.0.0
- **继续开发后续的新功能，正在此时，你突然接到一个电话说有个很严重的问题需要紧急修补， 你将按照如下方式来处理：**
  - 切换到tag v1.0.0的版本，并且创建一个分支hotfix
- **想要新建一个分支并同时切换到那个分支上，你可以运行一个带有 -b 参数的 git checkout 命令：**

```js
git checkout –b hotfix
//具体来说，git checkout 命令用于切换分支或恢复文件。-b 参数表示创建新分支，而 hotfix 是新分支的名称。因此，该命令的完整含义是创建名为 hotfix 的新分支，并切换到该分支。

//通常，开发人员会创建新分支来修复现有的 bug 或其他问题，以便在不影响主分支（通常是 master 分支）的情况下进行修改。然后，一旦修复完成并进行测试，他们可以将更改合并回主分支。
```

![image-20230222201851516](.\node_webpack_git_image\image-20230222201851516.png)![image-20230222201854594](.\node_webpack_git_image\image-20230222201854594.png)

### 分支开发和合并

- **分支上开发、修复bug：**
  - 我们可以在创建的hotfix分支上继续开发工作或者修复bug
  - 当完成要做的工作后，重新打上一个新的tag v1.0.1
- **切换回master分支，但是这个时候master分支也需要修复刚刚的bug：**
  - 所以我们需要将master分支和hotfix分支进行合并

```js
git checkout master//切换到master分支
git merge hotfix//将分支hotfix合并到master
```

<img src="D:\Desktop\文件夹统一存放\小余知识库\编程类笔记\JavaScript高级笔记(coderwhy) -- 原创\coderwhy-images\image-20230222202039988.png" style="zoom:120%;" />![](.\node_webpack_git_image\image-20230222202042676.png)

- 合并冲突

  - 解决方案1:把冲突的地方删一删，然后重新提交一下

  - 解决方案2：注意下图最上方的四个选项

  - | Accept Current Change  | 接收master里的代码 |
    | ---------------------- | ------------------ |
    | Accept Incoming Change | 接收合并进来的代码 |
    | Accept Both Changes    | 两个代码都保留下来 |
    | Compare Changes        | 比较两段代码的区别 |

![image-20230222203402069](.\node_webpack_git_image\image-20230222203402069.png)

> 第四个选项比较代码图

![image-20230222203802001](.\node_webpack_git_image\image-20230222203802001.png)

### 查看和删除分支

- **如果我们希望查看当前所有的分支，可以通过以下命令：**

```
git branch # 查看当前所有的分支
git branch –v # 同时查看最后一次提交
git branch --merged # 查看所有合并到当前分支的分支
git branch --no-merged # 查看所有没有合并到当前分支的分支
```

![image-20230222202202805](.\node_webpack_git_image\image-20230222202202805.png)

- **如果某些已经合并的分支我们不再需要了，那么可以将其移除掉：**

```apl
git branch –d hotfix # 删除当前分支(其实就是移除指针)
git branch –D hotfix # 强制删除某一个分支
```

## (理解)Git分支-Git的工作流中常见的分支

### Git的工作流（git flow）

- **由于Git上分支的使用的便捷性，产生了很多Git的工作流：**
  - 也就是说，在整个项目开发周期的不同阶段，你可以同时拥有多个开放的分支
  - 你可以定期地把某些主题分支合并入其他分支中
- **比如以下的工作流：**
  - master作为主分支
  - develop作为开发分支，并且有稳定版本时，合并到master分支中
  - topic作为某一个主题或者功能或者特性的分支进行开发(不知道能不能做到，单独进行拆分，如果不行就快速移除掉，可以就能够合并)，开发完成后合并到develop分支中

![image-20230222202317018](.\node_webpack_git_image\image-20230222202317018.png)

### 比较常见的git flow

![image-20230222202326772](.\node_webpack_git_image\image-20230222202326772.png)

## (掌握)Git分支-远程分支的使用操作

### Git的远程分支

- **远程分支是也是一种分支结构：**

  - 以 <remote>/<branch> 的形式命名的

  ```js
  //添加远程仓库
  git remote add origin xxxx
  //建立本地跟远程仓库的关系，通过上游方式
  git pull/push 上游分支//使本地master分支去跟远程仓库main分支。这样在我们git merge类似操作的时候，看似操作master，其实是操作上游分支
  ```

  

- **如果我们刚刚clone下来代码，分支的结构如下：**

- **如果其他人修改了代码，那么远程分支结构如下：**

  - 你需要通过fetch来获取最新的远程分支提交信息

![image-20230222202508918](.\node_webpack_git_image\image-20230222202508918.png)

![image-20230222202512583](.\node_webpack_git_image\image-20230222202512583.png)

![image-20230222202516184](.\node_webpack_git_image\image-20230222202516184.png)

### 远程分支的管理

- **操作一：推送分支到远程**

  - 当你想要公开分享一个分支时，需要将其推送到有写入权限的远程仓库上

  - ```js
    运行 git push <remote> <branch>
    //例如
    git push origin <branch>
    ```

- **操作二：跟踪远程分支**
  - 当克隆一个仓库时，它通常会自动地创建一个跟踪 origin/master 的 master 分支
  - 如果你愿意的话可以设置其他的跟踪分支，可以通过运行 git checkout --track <remote>/<branch>
  - 如果你尝试检出的分支 (a) 不存在且 (b) 刚好只有一个名字与之匹配的远程分支，那么 Git 就会为你创建一个跟踪分支

```js
git checkout --track <remote>/<branch>
//从远程仓库（remote）检出（checkout）一个新的本地分支（branch）并将其与远程分（remote/<branch>）关联起来
git checkout <branch>
//切换到指定的本地分支（branch），并将工作目录和暂存区更新为该分支的最新状态。如果该分支不存在，则会报错。这个命令也可以用于切换到之前创建的与远程分支关联的本地分支
```

- **操作三：删除远程分支**
  - 如果某一个远程分支不再使用，我们想要删除掉，可以运行带有 --delete 选项的 git push 命令来删除一个远程分支

```
git push origin --delete <branch>
```

## (了解)Git分支合并-rebase和merge的区别

### Git rebase用法

- **在 Git 中整合来自不同分支的修改主要有两种方法：merge 以及 rebase**

![image-20230222204555895](.\node_webpack_git_image\image-20230222204555895.png)

![image-20230222204559843](.\node_webpack_git_image\image-20230222204559843.png)

- **什么是rebase呢？**
  - 在上面的图例中，你可以提取在 C4 中引入的补丁和修改，然后在 C3 的基础上应用一次
  - 在 Git 中，这种操作就叫做 变基（rebase）
  - 你可以使用 rebase 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样
  - rebase这个单词如何理解呢？
    - 我们可以将其理解成改变当前分支的base
    - 比如在分支experiment上执行rebase master，那么可以改变experiment的base为master

```js
git checkout experiment
//作用是将当前工作目录和暂存区切换到名为experiment的本地分支，即切换到experiment分支
git rebase master
//作用是将当前分支（即experiment分支）在基于master分支的基础上进行变基（rebase）。具体来说，这个命令会将experiment分支上的提交历史重新应用到master分支的顶部，使得experiment分支的提交历史变得更加线性、整洁
```

### rebase的原理

> 达成效果前

![image-20230222214647948](.\node_webpack_git_image\image-20230222214647948.png)

> 达成效果后，更加简洁

![image-20230222214704146](.\node_webpack_git_image\image-20230222214704146.png)

- **rebase是如何工作的呢？**
  - 它的原理是首先找到这两个分支（即当前分支 experiment、变基操作的目标基底分支 master） 的最近共同祖先 C2
  - 然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件
  - 然后将当前分支指向目标基底 C3
  - 最后以此将之前另存为临时文件的修改依序应用
- 我们可以再次执行master上的合并操作：

```
$ git checkout master
$ git merge experiment
```

![image-20230222204902254](.\node_webpack_git_image\image-20230222204902254.png)

### rebase和merge的选择

- **开发中对于rebase和merge应该如何选择呢？**
- **事实上，rebase和merge是对Git历史的不同处理方法：**
  - **merge**用于**记录git的所有历史**，那么分支的历史错综复杂，也全部记录下来
  - **rebase**用于**简化历史记录**，将两个分支的历史简化，整个历史更加简洁
- **了解了rebase的底层原理，就可以根据自己的特定场景选择merge或者rebase**
- **注意：rebase有一条黄金法则：永远不要在主分支上使用rebase**
  - 如果**在main上面使用rebase，会造成大量的提交历史在main分支中不同**
  - 而多人开发时，其他人依然在原来的main中，对于提交历史来说会有很大的变化

![image-20230222205231820](.\node_webpack_git_image\image-20230222205231820.png)

### Git常见命令速查表

![image-20230222205244411](.\node_webpack_git_image\image-20230222205244411.png)

