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
执行这条命令后就会打开你所选择的文本编辑器。（默认会采用 shell 的环境变量 $EDITOR 所指定的文本编辑器，通常是 Vim 或者 Emacs。你也可以用第 1 章中所见到的 git config --global core.editor 命令配置 Git 使用任何你想要的编辑器。）
可以看出，默认的提交信息包含被注释掉的 git status 命令你的最新输出结果，在最上边还有一行是空行。你既可以删掉这些注释并输入自己的提交信息，也可以保留这些注释，以帮助你记住提交的具体内容。（若需要记下更详细的更改记录，可以给 git commit 加上 -v 参数。这样会把这次提交的差异比对显示在文本编辑器中，让你可以看到要提交的具体变更。）当你退出编辑器时，Git 会移除注释内容和差异比对，把剩下的提交信息记录到所创建的提交中。
完成上述提交还有另一种方式，那就是直接在命令行上键入提交信息。这会需要给 git commit 命令加上 -m 选项：
```shell
git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
2 files changed, 2 insertions(+)
create mode 10064 README
```
你终于完成了自己的首次提交。可以看到命令输出中包含了和该提交本身相关的一些信息：提交到哪个分支（master）、提交的 SHA-1 校验和是多少（463dc4f）、改动了多少个文件以及源文件新增和删除了多少行的统计信息。
请记住，提交时记录的是是暂存区中的快照。任何未暂存的内容仍然保持着已修改状态。你可以再次提交这些内容，将其纳入到版本历史记录中。每次提交时，都记录了项目的快照，日后可以用于比对或恢复。
## 2.2.8 跳过暂存区

在按照你的要求精确地生成提交内容时，暂存区非常有用，但就工作流而言，它有时显得有点过于繁琐了。如果你想要跳过暂存区直接提交，Git 为你提供了更快捷的途径。给 git commit 命令传入 -a 选项，就能让 Git 自动把已跟踪的所有文件添加到暂存区，然后在提交，这样你就不用再执行 git add 了：
```shell
git status
On branch master
Changes not staged for commit:
 (use "git add <file>..." to update what will be commited)
 (use "git checkout -- <file>..." to discard changes in working directory)
 
 modified: CONTRIBUTING.md
 
no changes added to commit (use "git add" and/or "git commit =a")

git commit -a -m 'added new benchmarks'
[master 83e38c7] added new benchmarks
1 file changed, 5 insertions(+), 0 deletions(-)
```
注意在上面的例子中，提交前不再需要执行 git add 来添加 CONTRIBUTING.md 文件了。
## 2.2.9 移除文件

要从 Git 中移除某个文件，你需要把它先从已跟踪文件列表中移除（确切地说，是从暂存区中移除），然后再提交。git rm 会帮你完成这些操作，另外该命令还会把文件从工作目录中移除，这样下一次你就不会在未跟踪文件列表中看到这些文件了。
如果你只是简单地把文件从你的工作目录移除，而没有使用 git rm，那么在执行 git status 时会看到文件出现在 "Changes not staged for commit" 区域（也就是未暂存区域）：
```shell
rm PROJECTS.md
git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
	(use "git add/rm <file>..." to update what will be commited)
	(use "git checkout -- <file>..." to discard changes in working directory)
	
	deleted: PROJECTS.md
	
no changes added to commit (use "git add" and/or "git commit -a")
```
如果你这时执行 git rm，Git 才会把文件的移除状态记录到暂存区：
```shell
git rm PROJECTS.md
rm 'PROJECTS.md'

git status
On branch master
Changes to be commited:
	(use "git reset HEAD <file>..." to unstage)
	
	deleted: PROJECTS.md
```
下一次提交的时候，这个文件就不存在了，也不会再给 Git 跟踪管理。如果你更改了某个文件，并已经把它加入到了索引当中（已暂存），要想让 Git 移除它就必须使用 -f 选项强制移除。这是为了防止没有被记录到快照中的数据被意外移除而设立的安全特性，因为这样的数据被意外移除后无法由 Git 恢复。
另一件你可能想做做的有用的事情是把文件保留在工作目录，但从暂存区中移除该文件。换句话说，你也许想将文件保留在硬盘上，但不想让 Git 对其进行跟踪管理。如果你忘了向 .gitignore 文件中添加相应的规则，不小心把一个很大的日志文件或者一些编译生成的 .a 文件添加进来，上述做法尤其有用。只需使用 --cached 选项即可：
```shell
git rm --cached README
```
你可以将文件、目录和文件的 glob 模式传递给 git rm 命令。这意味着你可以像下面这样：
```shell
git rm log/\*.log
```
请注意在 * 前面的反斜杠（\）是必需的，这是因为 shell 和 Git 先后都要处理文件名扩展。上述命令会移除 log 目录中所有扩展名为 .log 的文件。或者，你也可以像下面这样：
```shell
git rm \*~
```
这条命令会移除所有以~结尾的文件。
## 2.2.10 移动文件

Git 与很多其他版本控制系统不同，它并不会显式跟踪文件的移动。如果你在 Git 中重命名了文件，仓库的元数据并不会记录这次重命名操作。不过 Git 非常聪明，它能推断出究竟发生了什么。至于 Git 究竟如何检测到文件的移动操作，我们稍后再谈。
因此，当你看到 Git 有一个 mv 命令时就会有点搞不明白了。在 Git 中可以执行下面的命令重命令文件：
```shell
git mv file_from file_to
```
结果没有问题。实际上，执行了这条命令再去查看状态的话，就会发现 Git 识别出了重命令后的文件：
```shell
git mv README.md README
git status
Changes to be commited:
	(use "git reset HEAD <file>..." to unstage)
	
	renamed: README.md -> README
```
其实这相当于执行了下面的三条命令：
```shell
mv README.md README
git rm README.md
git add README
```
不管你是用 Git 的 mv 命令，还是直接给文件改名，Git 都能推断出这是重命名操作。唯一的区别是 git mv 只需键入一条命令而不是三条命令，所以会比较方便。更重要的是，你可以用任何你习惯的工具或方法来重命名文件，然后在提交之前再执行 Git 的 add 和 rm 命令。
# 2.3 查看提交历史

在完成了几次提交，或者克隆了一个已有提交历史的仓库之后，你可能想要看看历史记录。可以使用 git log 命令来实现，这是最基础却又最强大的一条命令。
下面这些例子要用到一个非常简单的示例项目 simplegit。要获取这个项目，请执行：
```shell
git clone https://github.com/schacon/simplegit-progit
```
当你在此项目中执行 git log 时，会得到以下输出：
```shell
git log
commit f9e1d7d00c67f95be5da7344afcbaf837fa51cb4 (HEAD -> 精通Git（第2版）, origin/精通Git（第2版）)
Author: wangqihao <w-qh@foxmail.com>
Date:   Wed Jun 24 16:00:04 2026 +0800

    第2章：Git 基础

commit 5867505cfdd639521c1df0c53eaf4c73661b6860
Author: wangqihao <w-qh@foxmail.com>
Date:   Wed Jun 24 12:07:11 2026 +0800

    第2章：Git 基础

commit 87e3bae0431437e92d827144bd229889ff59d372
Author: wangqihao <w-qh@foxmail.com>
Date:   Tue Jun 23 20:42:02 2026 +0800

    第2章：Git 基础
```
默认不加参数的情况下，git log 会按照时间顺序列出仓库中的所有提交，其中最新的提交显示在最前面。如你所见，和每个提交一同列出的还有它的 SHA-1 校验和、作者的姓名和邮箱、提交日期以及提交信息。
git log 有很多不同的选项，可以直观地展示出所需的内容。现在我们来看一些最常用的选项。
最有用的一个选项是 -p，它会显示出每次提交所引入的差异。你还可以加上 -2 参数，只输出最近的两次提交：
```shell
git log -p -2
```
加上这个选项之后，仍会显示原来的信息，不同之处是每一条提交记录后面带有 diff 信息。在代码审查或是快递浏览某个项目参与者的一系列提交时，这个选项会非常有用。git log 还提供了一系列的摘要选项。例如，可以用 --stat 选项来查看每个提交的简要统计信息：
```shell
git log --stat
```
如你所见，--stat 选项会在每个提交下面列出如下内容：改动的文件列表、共有多少文件被改动以及文件里有多少新增行或删除行。另外还会在最后输出总计信息。
另外一个颇为有用的选项是 --pretty，它可以更改日志输出的默认格式。Git 有一些预置的格式可供你选择。例如，在浏览大量提交时，oneline 格式选项很有用，它可以在每一行中显示一个提交。除此之外，short、full 和 fuller 格式选项分别比默认输出减少或增加一些信息：
```shell
git log --pretty=oneline
```
最值得注意的选项是 format，它允许你指定自己的输出格式。这样的输出特别有助于机器解析，因为你可以明确指定输出格式，其结果不会随着 Git 软件版本更新而改变：
```shell
git log --pretty=format:"%h - %an, %ar : %s"
```
下表列举了一些有用的格式选项。
git log --pretty=format 命令的一些有用的选项

| 格式选项 | 输出的格式描述                  |
| ---- | ------------------------ |
| %H   | 提交对象的散列值                 |
| %h   | 提交对象的简短散列值               |
| %T   | 树对象的散列值                  |
| %t   | 树对象的简短散列值                |
| %P   | 父对象的散列值                  |
| %p   | 父对象的简短散列值                |
| %an  | 作者的名字                    |
| %ae  | 作者的电子邮箱地址                |
| %ad  | 创作日期（可使用 -date=选项指定日期格式） |
| %ar  | 相对于当前日期的创作日期             |
| %cn  | 提交者的名字                   |
| %ce  | 提交者的电子邮箱地址               |
| %cd  | 提交日期                     |
| %cr  | 相对于当前日期的提交日期             |
| %s   | 提交信息的主题                  |
你可能不太清楚作者（author）和提交者（commiter）的区别是什么。作者是最初开展工作的人，而提交者则是最后将工作成果提交的人。所以，如果你为某个项目提交了补丁，随后项目的一位核心成员应用了该补丁，那么你和这位核心成员都会得到认可：你作为作者，核心成员作为提交者。我们将会在第 5 章中再细述其中的差别。
oneline 和 format 这两个选项如果与 log 命令的另一个选项 --graph 一起使用，就能发挥更大的作用。具体来说，--graph 选项会用 ASCII 字符形式的简单图表来显示 Git 分支和合并历史，如下所示。
```shell
git log --pretty=format:"%h %s" --graph
```
在学习了第 3 章的 Git 分支和合并机制之后，再来看以上输出，你会有更进一步的理解。
上面我们只讲解了 git log 的一些简单的输出格式选项，实际上还有很多其他选项可供使用。下表列举出的选项包括了我们已经讲解的和其他一些常用的格式选项及其效果。
git log 的常用选项

| 选项              | 描述                                                            |
| --------------- | ------------------------------------------------------------- |
| -p              | 按补丁格式显示每个提交引入的更改                                              |
| --stat          | 显示每个提交中被更改的文件的统计信息                                            |
| --shortstat     | 只显示上述 --stat 输出中包含"已更改/新增/删除"行的统计信息                           |
| --name-only     | 在每个提交信息后显示被更改的文件列表                                            |
| --name-status   | 在上一个选项输出基础上还显示出"已更改/新增/删除"统计信息                                |
| --abbrev-commit | 只显示完整的 SHA-1 40 位校验和字符串中的前几个字符                                |
| --relative-date | 显示相对日期（例如"两周前"），而不是完整日期                                       |
| --graph         | 在提交历史旁边显示 ASCII 图表，用于展示分支和合并的历史信息                             |
| --pretty        | 用一种可选格式显示提交。选项有 oneline、short、full、fuller 和 format（用于指定自定义格式） |
## 限制提交历史的输出范围

git log 除了有输出格式的选项之外还有其他一些有用的限制选项，这些选项可以只显示出部分提交。你已经见过了其中一个这样的选项，也就是 -2 选项，它可以只显示最近的两次提交。一般来说，你可以用 `-<n>` 显示最新的 n 次提交，其中 n 是任意整数。不过这个选项在 Git 实际应用中并不常用，因为默认情况下 Git 的所有输出会通过管道机制输入给分页程序（pager），使得一次只显示出一页内容。
与上述选项不同，像 --since 和 --until 这样按照时间限制输出的选项是十分有用的。例如，下面的命令会列举出最近两周内的所有提交：
```shell
git log --since=2.weeks
```
这条命令可以使用不同的时间形式，比如使用像 "2008-01-15" 这样的某个具体日期，或者类似 "2 years 1 day 3 minutes ago"（两年零一天零三分钟前）这样的相对时间。
你还可以按照某种搜索条件过滤提交列表。--author 选项允许你只查找某位作者的提交，--grep 选项则能让你搜索提交信息中的关键字（如果要同时指定 author 和 grep 选项， 则需要添加 --all-match 参数，否则 Git 会列出所有符合任何单一条件的提交）。
另外一个非常有用的过滤条件是 -S 选项，它后面需要接上一个字符串参数。这个选项只输出那些添加或者删除指定字符串的提交。举个例子，如果你想查找添加或删除了特定函数引用的最近一次提交，可以执行以下命令：
```shell
git log -Sfunction_name
```
最后一个很有用的 git log 过滤选项是文件路径。如果你指定了一个目录名或者文件名，就可以只输出更改了指定文件的那些提交。这个选项总是作为最后一个命令选项出现，通常需要在之前加两个短横线（--）来将路径和其他选项分隔开。
下表列举出上述选项以及一些其他常用选项以供参考。
表2-3 用于限制 git log 输出范围的选项

| 选项               | 描述                      |
| ---------------- | ----------------------- |
| -(n)             | 只显示最新的 n 次提交            |
| --since，--after  | 只输出指定日期之后的提交            |
| --until，--before | 只输出指定日期之前的提交            |
| --author         | 只输出作者与指定字符串匹配的提交        |
| --committer      | 只输出提交者与指定字符串的提交         |
| --grep           | 只输出提交信息包含指定字符串的提价       |
| -S               | 只输出包含"添加或删除指定字符串"的更改的提交 |
再举个例子，如果在 Git 项目历史中查看 Junio Hamano 在 2008 年 10 月发起的哪些提交更改了源代码中的测试文件且没有合并，可以执行以下命令：
```shell
git log --pretty="%h - %s" --author=gitster --since="2008-10-01" \
--before="2008-11-01" --no-merges -- t
```
在 Git 源代码仓库的近 40000 次历史提交中，这条命令只显示出符合上述条件的 6 次提交。
# 2.4 撤销操作

在任何时刻，你都有可能想撤销之前的操作。现在让我们来看看撤销更改所用到的几个基本工具。有些撤销操作是不可逆的，所以请务必当心。在 Git 中，误操作导致彻底丢失工作成果的情景并不多见，而这就是其中之一。
有一种撤销操作的常见使用场景是提交之后才发现自己忘了添加某些文件，或者写错了提交信息。如果这时你想重新尝试提交，可以使用 --amend 选项：
```shell
git commit --amend
```
上述命令会提交暂存区的内容。如果你在上次提交之后并没做出任何改动（比如你在上次提交后立即执行上面的命令），那么你的提交快照就不会有变化，但你可以改动提交信息。
和之前提交时一样，这次也会启动提交信息编辑器，有所不同的是打开的编辑器中会显示上次提交的信息。你可以像之前一样编辑，保存后就会覆盖之前的提交信息。
举一个例子，如果你提交后才意识到忘记了添加某个之前更改过的文件，可以执行类似下面的操作：
```shell
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```
最终只是产生了一个提交，因为第二个提交命令修正了第一个提交的结果。






























