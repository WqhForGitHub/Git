几乎每个版本控制系统都支持某种形式的分支功能。分支意味着偏离开发主线并继续你自己的工作而不影响主线开发。在很多版本控制工具中，这么做存在着较为昂贵的成本，因为常常需要去对整个源代码目录进行一次复制，而对于大型项目，这种复制会耗去很长时间。
有些人把 Git 的分支模型称为 Git 的杀手锏特性，而这项特性也确实使得 Git 从众多版本控制系统中脱颖而出。为什么它如此出众呢？实际上，Git 的分支功能轻量到了极致，以至于有关分支的操作几乎是即时完成的，并且在不同分支之间切换基本上也同样迅速。与其他版本控制系统不同，Git 鼓励在工作流中频繁说干分支与合并操作，甚至可以在一天中多次使用。理解并掌握 Git 的这一特性，就拥有了一件独一无二的强大工具，它可以彻底改变你的开发方式。
# 3.1 分支机制简述

要想真正理解 Git 的分支机制，我们要首先回过头来看一下 Git 是如何存储数据的。
正如第 1 章所述，Git 并没有采用多个变更集（changeset）或是差异的方式存储数据，而是采用一系列快照的方式。
当你发起提交时，Git 存储的是提交对象（commit object），其中包含了指向暂存区快照的指针。提交对象也包括作者姓名和邮箱地址、已输入的提交信息以及指向其父提交的指针。初始提交没有父提交，而一般的提交会有一个父提交。对于两个或更多分支的合并提交来说，存在着多个父提交。
为了把上述内容形象化，让我们假设有一个包含了三个文件的目录，而你把这些文件都加入到了暂存区并进行了提交。暂存操作会为每个文件计算校验和（即第 1 章提到过的 SHA-1 散列值），并把文件的当前版本保存到 Git 仓库中（Git 把这些数据叫做 blob 对象），然后把校验和添加到暂存区：
```shell
git add README test.rb LICENSE
git commit -m 'The initial commit of my project'
```
当执行 git commit 进行提交时，Git 会先为每个子目录计算校验和（在本例中只有项目的根目录），然后再把这些树对象（tree object）保存到 Git 仓库中。Git 随后会创建提交对象，其中包括元数据以及指向项目根目录的树对象的指针，以便有需要的时候重新创建这次快照。
现在 Git 仓库中包含了 5 个对象：3 个 blob 对象（分别保存了你的 3 个文件的内容）、1 个树对象（记录着目录结构以及 blob 对象和文件名之间的对应关系）以及 1 个提交对象（包含着提交的全部元数据和指向根目录树对象的指针）。
如果你又做了一些更改，并又进行了一次提交，这第二次提交就会保存着指向它的上一次提交的指针。
Git 的分支只不过是一个指向某次提交的轻量级的可移动指针（movable pointer）。Git 默认的分支名称是 master。当你发起提交时，就有了一个指向最后一次提交的 master 分支。每次提交时，它都会自动向前移动。
>注意
>在 Git 中，master 分支其实并不是一个特殊的分支，它与其他分支没什么区别。几乎每个 Git 仓库都拥有该分支，这只是因为 git init 命令会默认创建该分支，而大多数人都懒得去更改它。
## 3.1.1 创建新分支

当你创建新分支时会发生什么？实际上，Git 会创建一个可移动的新指针供你使用。现在假设你要创建一个名为 testing 的新分支。这可以通过 git branch 命令实现：
```shell
git branch testing
```
这会创建一个指向当前提交的新指针。
![指向同一系列提交的两个分支](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E6%8C%87%E5%90%91%E5%90%8C%E4%B8%80%E7%B3%BB%E5%88%97%E6%8F%90%E4%BA%A4%E7%9A%84%E4%B8%A4%E4%B8%AA%E5%88%86%E6%94%AF.png)
Git 如何知道你当前处在哪一分支上呢？实际上 Git 维护着一个名为 HEAD 的特殊指针。请注意，这里 HEAD 的概念与你可能了解的其他版本控制系统（例如 Subversion 或 CVS）中的 HEAD 有着很大的不同。在 Git 中，HEAD 是一个指向当前所在的本地分支的指针。在上述例子中，你仍然处在 master 分支上。这是因为 git branch 命令只会创建新分支，而不会切换到新的分支上去。
![指向某分支的HEAD指针](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E6%8C%87%E5%90%91%E6%9F%90%E5%88%86%E6%94%AF%E7%9A%84%20HEAD%20%E6%8C%87%E9%92%88.png)
可以简单地通过 git log 命令查看各个分支当前所指向的对象。这需要用到选项 --decorate：
```shell
git log --oneline --decorate
f30ab（HEAD，master，testing）add feature #32 - ability to add new
34ac2 fixed bug #132B - stack overflow under certain conditions
98ca9 initial commit of my project
```
可以看到，master 和 testing 分支就显示在 f30ab 提交旁边。
## 3.1.2 切换分支

要切换到已有的分支，可以执行 git checkout 命令。现在让我们切换到新建的 testing 分支上去，如下所示。
```shell
git checkout testing
```
这条命令会改变 HEAD 指针，使其指向 testizng 分支。
![指向当前分支的HEAD指针](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E6%8C%87%E5%90%91%E5%BD%93%E5%89%8D%E5%88%86%E6%94%AF%E7%9A%84%20HEAD%20%E6%8C%87%E9%92%88.png)
这么做意义何在？好，现在让我们再提交一次：
```shell
vim test.rb
git commit -a -m 'made a change'
```
![当有新的提交时，HEAD指针会向前移动](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E5%BD%93%E6%9C%89%E6%96%B0%E7%9A%84%E6%8F%90%E4%BA%A4%E6%97%B6%EF%BC%8CHEAD%20%E6%8C%87%E9%92%88%E4%BC%9A%E5%90%91%E5%89%8D%E7%A7%BB%E5%8A%A8.png)
现在就有意思了：testing 分支已经向前移动，然而 master 分支仍然指向你之前执行 git checkout 切换分支时所在的提交。让我们再切换到 master 分支：
```shell
git checkout master
```
![当切换分支时，HEAD指针会随之移动](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E5%BD%93%E5%88%87%E6%8D%A2%E5%88%86%E6%94%AF%E6%97%B6%EF%BC%8CHEAD%E6%8C%87%E9%92%88%E4%BC%9A%E9%9A%8F%E4%B9%8B%E7%A7%BB%E5%8A%A8.png)
以上命令一共做了两件事情。它会把 HEAD 指针移回到 master 分支，还会把工作目录的文件恢复到 master 分支指向的快照的状态。这也就意味着，从这时起，你所做出的修改将基于项目的较老版本。总而言之，上述操作回滚了你在 testing 分支上所做的工作，使你能向另一个方向进行开发工作。
>分支切换会更改工作目录文件
>请注意，当你在 Git 中切换分支时，工作目录的文件会被改变。如果你切换到较旧的分支，工作目录会被恢复到该分支上最后一次提交的状态。如果 Git 在当前状态下无法干净地完成恢复操作，就不会允许你切换分支。

让我们做出一些改动，再提交一次，如下所示。
```shell
vim test.rb
git commit -a -m 'made other changes'
```
现在项目历史已经产生了分叉。你创建并切换到了新的分支，在新分支上做了一次修改，然后又切换回你的主分支并做了另一次修改。这两次修改是在不同的分支上做出的，彼此互相分离。你可以在分支间自由切换，当你准备好了之后就可以合并这些修改。只需使用简单的 branch、checkout 和 commit 命令就实现了上述操作。
![有分叉的项目历史](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E6%9C%89%E5%88%86%E5%8F%89%E7%9A%84%E9%A1%B9%E7%9B%AE%E5%8E%86%E5%8F%B2.png)
使用 git log 命令也可以很容易地注意到分叉的历史。git log --oneline --decorate --graph --all 命令会输出提交历史，显示出分支的指向以及项目历史的分叉情况。
```shell
git log --oneline --decorate --graph --all
```
Git 中的分支实际上就是一个简单的文件，其中只包含了该分支所指向提交的长度为 40 个字符的 SHA-1 校验和。正因为如此，Git 分支创建和删除的成本很低。创建新分支就如同向文件写入 41 个字节（40 个字符外加一个换行符）一样又简单又快速。
这样的效率与其他大多数较老的版本控制系统处理分支的方式形成了鲜明对比。其他系统大多会把整个项目的所有文件复制到一个新的目录中。根据项目的大小，这样的操作会花费几秒钟甚至几分钟的时间。与之相反，在 Git 中分支操作几乎都是即刻完成的。而且，由于提交时 Git 保存了父对象的指针，当进行合并操作时 Git 会自动寻找适当的合并基础，操作起来非常简单。有了上述特性作为保障，Git 鼓励开发人员经常创建和使用分支。
让我们来看看为什么你应该这样做。
# 3.2 基本的分支与合并操作

现在我们要展示一个简单的分支和合并案例，其中的工作流可供真实项目借鉴。要遵循的步骤如下：
（1）在网站展示工作
（2）为新需求创建分支
（3）在新分支上展开工作
这时，你接到一个电话，说项目有一个严重问题需要紧急修复。你随后会这样做：
（1）切换到你的生产环境分支
（2）创建新的分支来进行此次问题的热修补工作
（3）通过测试后，合并热修补分支并推送到生产环境中
（4）切换回之前的需求分支上继续工作
## 3.2.1 基本的分支操作

首先，假设你在所工作的项目上已经完成了一些提交。
![简单的提交历史](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E7%AE%80%E5%8D%95%E7%9A%84%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2.png)
这时，你决定要修复公司所用的问题跟踪系统中的 #53 问题。可以使用带有 -b 选项的 git checkout 命令来创建并切换到新分支上：
```shell
git checkout -b iss53
Switched to a new branch "iss53"
```
上面这条命令相当于：
```shell
git branch iss53
git checkout iss53
```
![创建新的分支指针](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E5%88%9B%E5%BB%BA%E6%96%B0%E7%9A%84%E5%88%86%E6%94%AF%E6%8C%87%E9%92%88.png)
接下来继续工作，并又进行了几次提交。这么做会让 iss53 分支指针向前移动，这是因为你当前检出的就是 iss53 分支（换句话说，HEAD 指针当前指向该分支）：
```shell
vim index.html
git commit -a -m 'added a new footer [issue 53]'
```
![iss53分支指针会随着工作进展而向前移动](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/iss53%E5%88%86%E6%94%AF%E6%8C%87%E9%92%88%E4%BC%9A%E9%9A%8F%E7%9D%80%E5%B7%A5%E4%BD%9C%E8%BF%9B%E5%B1%95%E8%80%8C%E5%90%91%E5%89%8D%E7%A7%BB%E5%8A%A8.png)
现在，你接到一个电话，说网站有个问题需要立即修复。如果没有 Git 的帮助，你要么把你的修复补丁和 iss53 的变更一起部署，要么就花费大量精力去恢复之前针对 iss53 所做的工作，好让你制作的修复补丁单独上线。如今你要做的就是切换回 master 分支即可。
但先别急，在你切换分支之前要注意的是，如果你的工作目录或者暂存区存在着未提交的更改，并且这些更改与你要切换到的分支冲突，Git 就不允许你切换分支。在切换分支时，最好是保持一个干净地工作区域。稍后我们会介绍几种绕过这个问题的办法：储藏和修订提交。就现在而言，让我们假定你已经提交了所有修改，这样你就可以切换回 master 分支了：
```shell
git checkout master
Switched to branch 'master'
```
此时项目的工作目录就与你开始处理 #53 问题之前的状态一模一样了，你就可以集中精力制作热补丁了。这里有一点需要强调：当你切换分支时，Git 会把工作目录恢复到你切换到的分支上最后一次提交时的状态。
接下来需要制作热补丁。让我们创建 hotfix 分支并在这个分支上展示修复工作：
```shell
git checkout -b hotfix
Switched to a new branch 'hotfix'
vim index.html
git commit -a -m 'fixed the broken email address'
[hotfix 1fb7853] fixed the broken email address
1 file changed, 2 insertions(+)
```
![由master分支分化出来的hotfix分支](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E7%94%B1master%E5%88%86%E6%94%AF%E5%88%86%E5%8C%96%E5%87%BA%E6%9D%A5%E7%9A%84hotfix%E5%88%86%E6%94%AF.png)
你可以运行测试来确保热补丁的效果无误，然后将其合并到 master 分支，以便部署到生产环境。使用 git merge 命令来完成上述操作：
```shell
git checkout master
git merge hotfix
Updating f42c576...3a0874c
Fast-forward
	index.html | 2 ++
	1 file changed, 2 insertions(+)
```
你会注意到合并时出现了 "fast-forward" 的提示。由于当前所在的 master 分支所指向的提交是要并入的 hotfix 分支的直接上游，因而 Git 会将 master 分支指针向前移动。换句话说，当你试图去合并两个不同的提交，而顺着其中一个提交的历史可以直接到达另一个提交时，Git 就会简化合并操作，直接把分支指针向前移动，因为这种单线历史不存在有分歧的工作。这就叫作 ”fast-forward“。
现在你的变更已经进入了 master 分支所指向的提交快照，可以部署补丁了。
![master分支被快进到hotfix分支](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/master%E5%88%86%E6%94%AF%E8%A2%AB%E5%BF%AB%E8%BF%9B%E5%88%B0hotfix%E5%88%86%E6%94%AF.png)
在部署了这次极其重要的热修复补丁之后，你准备要切换回之前被打断的工作上去。不过先别急，首先你要把已经用不着的 hotfix 分支删除，该分支和 master 分支指向的位置相同。使用 git branch 的 -d 选项来删除这个分支：
```shell
git branch -d hotfix
Deleted branch hotfix（3a0874c）.
```
现在你可以切换回之前未完成的 #53 问题分支，并且继续进行工作：
```shell
git checkout iss53
Switched to branch "iss53"
vim index.html
git commit -a -m 'finished the new footer [issue 53]'
[iss53 ad82d7a] finished the new footer [issue 53]
1 file changed, 1 insertion(+)
```
![继续iss53分支上的工作](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E7%BB%A7%E7%BB%ADiss53%E5%88%86%E6%94%AF%E4%B8%8A%E7%9A%84%E5%B7%A5%E4%BD%9C.png)
值得注意的是，iss53 分支并不包含你在 hotfix 分支上做过的工作。如果需要把上述修补工作并入 iss53，就需要执行 git merge master 使得 master 分支合并到 iss53 中，或者可以等到要把 iss53 合并回 master 分支时再把热修补的工作整合进来。
## 3.2.2 基本的合并操作

假设现在 #53 的工作已经完工，可以合并回 master 分支了。这次的合并操作实现起来与之前合并 hotfix 分支的操作差不多。只需要切换到 master 分支上，并执行 git merge 命令即可：
```shell
git checkout master
Switched to branch 'master'
git merge iss53
Merge made by the 'recursive' strategy.
index.html | 1 +
1 file changed, 1 insertion(+)
```
这次合并看起来与之前 hotfix 的合并有点不一样。在这次合并中，开发历史从某个早先的时间点开始有了分叉。由于当前 master 分支指向的提交并不是 iss53 分支的直接祖先，因而 Git 必须要做一些额外的工作。本例中，Git 执行的操作是简单的三方合并。三方合并操作会使用两个待合并分支上最新提交的快照，以及这两个分支的共同祖先的提交快照（如下图所示）。
![在一次典型的合并操作中用到的三个提交快照](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E5%9C%A8%E4%B8%80%E6%AC%A1%E5%85%B8%E5%9E%8B%E7%9A%84%E5%90%88%E5%B9%B6%E6%93%8D%E4%BD%9C%E4%B8%AD%E7%94%A8%E5%88%B0%E7%9A%84%E4%B8%89%E4%B8%AA%E6%8F%90%E4%BA%A4%E5%BF%AB%E7%85%A7.png)
与之前简单地向前移动分支指针的做法不同，这一次 Git 会基于三方合并的结果创建新的快照，然后再创建一个提交指向新建的快照。这个提交叫做合并提交。合并提交的特殊性在于它拥有不止一个父提交。
![合并提交](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC3%E7%AB%A0%EF%BC%9AGit%20%E5%88%86%E6%94%AF%E6%9C%BA%E5%88%B6/%E5%90%88%E5%B9%B6%E6%8F%90%E4%BA%A4.png)
值得注意的是，Git 会自己判断最优的共同祖先并将其作为合并基础。这种做法与诸如 CVS 或 Subversion（1.5 以前的版本）等较老的工具不同。在这些较老的工具中，开发者必须自己找出最优的合并基础，来执行合并操作。以上区别使得 Git 在合并操作方面比其他工具要简单得多。
现在你的工作成果已经合并进来了，你就不再需要 iss53 分支了。你可以在问题追踪系统里面关闭这个问题并删除分支。
```shell
git branch -d iss53
```
## 3.2.3 基本的合并冲突处理

有时候，上述合并过程并不会那么顺利。如果你在要合并的两个分支上都改了同一个文件的同一部分内容，Git 就没办法干净地合并这两个分支。假设你在 #53 问题上的工作和在 hotfix 分支上的工作都修改了同一文件的同一部分，那么就会引起合并冲突，你会看到类似下面的输出：
```shell
git merge iss53
Auto-merging index.html
CONFLICT（content）:Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```
Git 并没有自动创建新的合并提交。它会暂停整个合并过程，等待你来解决冲突。在发生了合并冲突后，要查看哪些文件没有被合并，可以执行 git status：
```shell
git status
On branch master
You have unmerged paths.
	(fix conflicts and run "git commit")

Unmerged paths:
	(use "git add <file>..." to mark resolution)
	
		both modified: index.html
		
no changes added to commit (use "git add" and/or "git commit -a")
```
任何存在着未解决的合并冲突的文件都会显示成未合并状态。Git 会给这些有冲突的文件添加标准的待解决冲突标记，以便你手动打开这些文件来解决冲突。可以看到冲突文件包含一个类似下面这样的区域：
```html
<<<<<<< HEAD:index.html
<div id="footer">contact: email.support@github.com</div>
=======
<div id="footer">
	please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```
上面这段代码中，HEAD 版本的内容显示在上半部分（======= 以上的部分），iss53 分支的内容则在下半部分。其中 HEAD 指向的是 master 分支，因为你在执行 merge 命令之前已经切换到该分支。可以选择使用任一版本的内容或是自己整合两者的内容来解决冲突。例如，你可以把整段内容替换成以下代码：
```html
<div id="footer">
please contact us at email.support@github.com
</div>
```







































































