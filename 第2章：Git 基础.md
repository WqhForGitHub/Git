如果只让你读一章内容就开始使用 Git，那么读这章就对了。本章涵盖了你在使用 Git 的绝大多数时间里会用到的所有基础命令。学完本章，你应该能够配置并初始化 Git 仓库、开始或停止跟踪文件、暂存或者提交更改。我们也会讲授如何让 Git 忽略某些文件和文件模式，如何简单快速地撤销错误操作，如何浏览项目版本历史并查看版本之间的差异，以及如何向远程仓库推送或从中拉取数据。
# 2.1 获取 Git 仓库

建立 Git 项目的方法主要有两种。第一种是把现有的项目或者目录导入到 Git 中，第二种是从服务器上克隆现有的 Git 仓库。
## 2.1.1 在现有目录中初始化 Git 仓库

要想在 Git 中对现有项目进行跟踪管理，只需进入项目目录并输入：
```shell
git init
```
这会创建一个名为 .git 的子目录。这个子目录包含了构成 Git 仓库骨架的所有必需文件。但此刻 Git 尚未跟踪项目中的任何文件。（有关 .git 目录中具体包含了哪些文件的详细信息，请参看第 10 章。）
如果你打算着手对现有文件（非空目录）进行版本控制，那么就应该开始跟踪这些文件并进行初次提交。对需要跟踪的文件执行几次 git add 命令，然后输入 git commit 命令即可：
```shell
git add *.c
git add LICENSE
git commit -m 'initial project version'
```
稍后我们会逐一解释这些命令的含义。现在，你的 Git 仓库已经包含了这些被跟踪的文件并进行了初次提交。
## 2.1.2 克隆现有仓库

如果需要获取现有仓库的一份副本（比如这里你想参与的一个项目），可以使用 git clone 命令。如果你熟悉其他版本控制系统（比如 Subversion），就会注意到这个命令是 clone 而不是 checkout。这是一个很重要的差异，因为 Git 会对服务器仓库的几乎所有数据进行完整复制，而不只是复制当前工作目录。git clone 默认会从服务器上把整个项目历史中每个文件的所有历史版本都拉取下来。实际上，如果你的服务器磁盘损坏，你通常可以用任何客户端计算机上的 Git 仓库副本恢复服务器【如果这样的话，服务器端的钩子设置（server-side hook）也许会丢失，但全部的版本数据都会恢复如初，第 4 章对此会有详述】。
克隆仓库需要使用 git clone [url] 命令。例如，要克隆 Git 的链接库 Libgit2，可以像下面这样做：
```shell
git clone https://github.com/libgit2/libgit2
```
这会创建一个名为 libgit2 的新目录，并在其中初始化 .git 目录，然后将远程仓库中的所有数据拉取到本地并检出最新版本的可用副本。进入新的 libgit2 目录中，会看到所有项目文件已经准备就绪。如果想将项目克隆到其他名字的目录中，可以把目录名作为命令行选项传入：
```shell
git clone https://github.com/libgit2/libgit2 mylibgit
```
这一条命令与上一条命令功能相同，只是目标目录的名称变成了 mylibgit。
Git 可以用几种不同的协议传输数据。上一个例子使用的是 https:// 协议，除此之外也可以使用 git:// 协议或者是 SSH 传输协议（如 user@server:path/to/repo.git。）第4章会讲到可以用来访问 Git 仓库的所有方法，并分析各自的优劣。
# 2.2 在 Git 仓库中记录变更

你现在拥有了一个真正的 Git 仓库并检出了项目文件的可用副本。下一步就是做出一些更改，当项目到达某个需要记录的状态时向仓库提交这些变更的快照。
请记住，工作目录下的每一个文件都处于两种状态之一：已跟踪（tracked）或未跟踪（untracked）。已跟踪的文件是指上一次快照中包含的文件。这些文件又可以分为未修改、已修改或已暂存三种状态。而未跟踪的文件则是工作目录中除去已跟踪文件之外的所有文件，也就是既不在上一次快照中，也不在暂存区中的文件。当你刚刚完成仓库克隆时，所有文件的状态都是已跟踪且未修改的，因为你刚刚把它们检出，而没有做出过任何改动。
如果修改了文件，它们在 Git 中的状态就会变成已修改，这意味着自从上次提交以来文件已经发生了变化。你接下来要把这些已修改的文件添加到暂存区，提交所有已暂存的变更，随后重复这个过程。
![文件状态的生命周期](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC2%E7%AB%A0%EF%BC%9AGit%20%E5%9F%BA%E7%A1%80/%E6%96%87%E4%BB%B6%E7%8A%B6%E6%80%81%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)
## 2.2.1 查看当前文件状态

检查文件所处状态的主要工具是 git status 命令。如果在克隆仓库后立即执行这个命令，就会看到类似下面的输出：

```shell
git status
On branch master
nothing to commit, working directory clean
```

上述输出说明你的项目工作目录是干净的。也就是说，工作目录下没有任何已跟踪的文件被修改过。Git 也没有找到任何未跟踪的文件，否则这些文件会被列出。最后，该命令还会显示当前所处的分支，告诉你现在所处的本地分支与服务器上的对应的分支没有出现偏离。就目前而言，我们一直处在默认的 master 分支。现在不需要担心分支问题，我们会在第 3 章详细讲述分支和引用。
现在，让我们把一个简单的 README 文件添加到项目中。如果之前项目中不存在这个文件，那么这次执行 git status 就会看到这个未跟踪的文件：

```shell
echo 'My Project' > README
git status
On branch master
Untracked files:
	(use "git add <file>..." to include in what will be committed)
	
		README
		
    nothing added to commit but untracked files present (use "git add" to track)
```
可以看到，新的 README 文件处于未跟踪状态，因为 git status 输出时把这个文件显示在 "Untracked files"（未跟踪的文件）条目下。未跟踪的文件就是 Git 在上一次快照（提交）中没有发现的文件。Git 不会主动把这些文件包含到下一次提交的文件范围中，除非你明确告诉 Git 你需要跟踪这些文件。这样做是为了避免你不小心把编译生成的二进制文件或者其他你不想跟踪的文件包含进来。需要让 Git 跟踪该文件，才能将 README 加入。

## 2.2.2 跟踪新文件

可以使用 git add 命令让 Git 开始跟踪新的文件。执行以下命令来跟踪 README 文件：

```shell
git add README
```

此时如果重新执行查看项目状态的命令，就可以看到 README 文件已处于跟踪状态，并被添加到暂存区等待提交：

```shell
git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new File:    README
```
在 "Changes to be commited"（等待提交的更改）标题下列出的就是已暂存的文件。如果现在提交，那么之前执行 git add 时的文件版本就会被添加到历史快照中。回想一下，在早先执行 git init 时，你执行的下一个命令就是 git add (files)，这条命令就是让 Git 开始跟踪工作目录下的文件。git add 命令接受一个文件或目录的路径名作为参数。如果提供的参数是目录，该命令会递归地添加该目录下的所有文件。

## 2.2.3 暂存已修改的文件
这次让我们来更改一个已跟踪的文件。假如你更改了之前已经被 Git 跟踪的 CONTRIBUTING.md 文件，此时再执行 git status 命令，会看到类似下面的输出：

```powershell
$ git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new File:   README
	
	Changes not staged for commit:
	(use "git add <file>..." to update what will be committed)
	(use "git checkout -- <file>..." to discard changes in working directory)
	 modified: CONTRIBUTING.md              
```
CONTRIBUTING.md 文件会出现在名为 "Changes not staged for commit"（已更改但未添加到暂存区）的区域中，这表示处于跟踪状态的文件在工作目录下已被更改，但尚未被添加到暂存区。要想暂存这些文件，需要执行 git add 命令。git add 是一个多功能命令，既可以用来跟踪新文件，也可以用来暂存文件，它还可以做其他的一些事，比如把存在合并冲突的文件标记为已解决。所以，把 git add 命令看成添加内容到下一次提交中而不是把这个文件加入到项目中，更有助于理解该命令。现在让我们执行 git add 命令，将 CONTRIBUTING.md 添加到暂存区，然后重新执行 git status：
```shell                                                                           
git add CONTRIBUTING.md
git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new file: README
	modified: CONTRIBUTING.md
```
上面列出的这两个文件都已暂存，并将进入下一个提交中。假设你在这时想起来在提交之前还要再对 CONTRIBUTING.md 做一个小小的修改。于是你打开文件，做出改动，然后准备提交。不过让我们先来再执行一次 git status：
```shell                                                                         
vim CONTRIBUTING.md
git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	new file: README
	modified: CONTRIBUTING.md
	
Changes not staged for commit:
	(use "git add <file>..." to update what will be committed)
	(use "git checkout -- <file>..." to discard changes in working directory)
	
	modified: CONTRIBUTING.md
```
这是怎么回事？现在 CONTRIBUTING.md 文件竟然同时出现在了已暂存和未暂存的列表中。这怎么可能呢？其实，在暂存一个文件时，Git 保存的是你执行 git add 时文件的样子。如果你现在执行 git commit 命令进行提交，包含在这次提交中的 CONTRIBUTING.md 是你上次执行 git add 命令时的文件版本，而不是现在工作目录中该文件的当前版本。所以，如果在执行了 git add 之后又对已添加到暂存区的文件做了修改，就需要再一次执行 git add 将文件的最新版本添加到暂存区：
```shell  
git add CONTRIBUTING.md
git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new file: README
	modified: CONTRIBUTING.md  
```

## 2.2.4 显示更简洁的状态信息

虽然 git status 命令的输出信息很全面，但也着实冗长。对此，Git 也提供了一个显示简短状态的命令行选项，使你可以以一种更为紧凑的形式查看变更。执行 git status -s 或者 git status --short 就可以看到类似下面的效果。
```shell
git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```
未被跟踪的新文件旁边会有一个 ?? 标记，已暂存的新文件会有 A 标记，而已修改的文件则会有一个 M 标记，等等。实际上，文件列表旁边的标记是分成两列的，左列标明了文件是否已暂存，而右列表明了文件是否已修改。以上面的命令行输出为例，工作目录下的 README 文件已被修改，但还没有被暂存。另一个 lib/simplegit.rb 问价是已修改而且已暂存的状态。而 Rakefile 文件则是已修改并被添加到暂存区，之后又被修改过，因此暂存区和工作区都包含了该文件的变更。
## 2.2.5 忽略文件

很多时候，你并不希望某一类文件被 Git 自动添加，甚至不想这些文件被显示在未跟踪的文件列表下面。这些文件一般是自动生成的文件（比如日志文件）或是由构建系统创建的文件。在这种情况下，可以创建名为 .gitignore 的文件，在其中列出待匹配文件的模式。下面是一个 .gitignore 文件的例子：
```shell
cat .gitignore
*.[oa]
*~
```
其中第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件，这些都是构建代码的过程中所生成的对象和归档文件。第二行则是告诉 Git 忽略所有以波浪号（~）结尾的文件，Emacs 等许多文本编辑器都会将其标记为临时文件。你也可以让 Git 忽略 log 目录、tmp 目录、pid 目录以及自动生成的文档等。最好在开始工作前配置好 .gitignore 文件，这样你就不会意外地把不想纳入 Git 仓库的文件提交进来了。
可以写入 .gitignore 文件中的匹配模式的规则如下：
- 空行或者以 # 开始的行会被忽略
- 支持标准的 glob 模式
- 以斜杠（/）开头的模式可用于禁止递归匹配
- 以斜杠（/）结尾的模式表示目录
- 以感叹号（!）开始的模式表示取反
glob 模式类似于 shell 所使用的简化版正则表达式。具体来讲，星号（`*`）匹配零个或更多字符，[abc]匹配方括号内的任意单个字符（在这个例子里是 a、b 或 c），而问号（?）则匹配任意单个字符。在方括号中使用使用短划线分隔两个字符（例如[0-9]）的模式能够匹配在这两个字符范围内的任何单个字符（在这个例子里是0到9之间的任何数字）。你还可以用两个星号匹配嵌套目录，比如 `a/**/z` 能够匹配 `a/z`、`a/b/z`和`a/b/c/z`等。
下面是另一个 .gitignore 文件的例子：
```shell
*.a # 忽略 .a 类型的文件
!lib.a # 仍然跟踪 lib.a，即使上一行指令要忽略 .a 类型的文件
/TODO # 只忽略当前目录的 TODO 文件，而不忽略子目录下的 TODO
build/ # 忽略 build/ 目录下的所有文件
doc/*.txt # 忽略 doc/notes.txt，而不忽略 doc/server/arch.txt
doc/**/*.pdf # 忽略 doc/目录下的所有 .pdf 文件
```
>注意
>GitHub 维护了一份相当全面的 .gitignore 参考示例列表，其中的例子都非常不错，涵盖了数十个不同项目和语言，可以作为自己项目的参考。
## 2.2.6 查看已暂存和未暂存的变更

如果 git status 命令的输出信息对你来说太过泛泛，你想知道修改的具体内容，而不仅仅是你更改了哪些文件，这时可以使用 git diff 命令。我们将稍后讲解 git diff 的细节，现在只需知道它基本上可以用来解决两个问题：哪些变更还没有被暂存？哪些已暂存的变更正待提交？尽管 git status 也可以通过列举文件名的方式大致回答上述问题，但 git diff 则会显示出你具体添加和删除了哪些行。换句话说，git diff 的输出是补丁（patch）。
假设你又编辑并暂存了 README 文件，之后更改了 CONTRIBUTING.md 但没有暂存它。如果你现在执行 git status 命令，那么又会看到类似下面的输出：
```shell
git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new file: README
	
Changes not staged for commit:
	(use "git add <file>..." to update what will be committed)
	(use "git checkout -- <file>..." to discard changes in working directory)
	
	modified: CONTRIBUTING.md
```
要查看尚未添加到暂存区的变更，直接输入不加参数的 git diff 命令：
```shell
git diff
```
## 2.2.7 提交变更

现在你的暂存区已经准备妥当，可以提交了。请记得所有未暂存的变更都不会进入到提交的内容中，这包括任何在编辑之后没有执行 git add 命令添加到暂存区的新建的或修改过的文件。这些文件在提交后状态并不会发生变化，仍然是已修改的状态。举个例子，假设你上次执行 git status 命令时看到所有变更都已暂存并等待提交。这时最简单的提交方式就是执行 git commit 命令：
```shell
git commit
```

