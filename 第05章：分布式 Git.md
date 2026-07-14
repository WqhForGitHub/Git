现在你已经设置好了一个可供全体开发人员共享代码的远程 Git 仓库，也熟悉了用于本地工作流的基本 Git 命令，接下来看看如何利用 Git 所提供的一些分布式工作流。
在本章中，你将学习到如何以贡献者和参与人员的身份在分布式环境中使用 Git。也就是说，要学习如何顺利地向项目贡献代码，尽可能地简化自己和项目维护人员的工作，以及如何妥当地维护由多人开发的项目。
# 5.1 分布式工作流

与集中式版本控制系统不同，Git 的分布式特性使得你在项目协作方式中拥有巨大的灵活性。在集中式系统中，每个开发人员基本上都是工作在中枢上的某个节点。但是在 Git 中，开发人员既可以是节点，也可以是中枢。意思就是说，自己在向其他仓库贡献代码的同时还能够维护公共仓库，以供他人使用和提交代码。这就为你的项目或团队展现出了各式各样可能的工作流，因此接下来我们会讲述几个利用了这种灵活性的常见范式。除此之外，还会讨论每种设计的优点及其潜在的缺点，你可以选择使用其中的某一种，或者从不同设计中混搭使用所需的功能。
## 5.1.1 集中式工作流

在集中式系统中，通常只有一种协作模型，即集中式工作流。一个中枢（或是仓库）接受代码，所有人以此同步各自的工作。大量开发人员作为节点（也就是中枢的用户），同步到同一个位置。
![集中式工作流](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/%E9%9B%86%E4%B8%AD%E5%BC%8F%E5%B7%A5%E4%BD%9C%E6%B5%81.png)
这意味着如果两名开发人员都从中枢克隆了代码并做出了各自的修改，只有第一个开发人员能够顺利地将变更推送回中枢。另一个开发人员在推送变更之前必须合并上一个开发人员的工作，这样才不至于覆盖前者的变更。这个概念在 Git 和 Subversion（或是其他任何集中式版本控制系统）中都一样，该模型也能够很好地运用在 Git 中。
如果你所在的公司或团队已经适应了集中式工作流，那么你完全可以在 Git 中继续沿用此模式。只需要设置单个仓库，赋予团队成员推送权限，Git 确保不会让用户之间出现相互覆盖的现象。假设 John 和 Jessica 同时开始工作。John 完成了修改并将变更推送到服务器。然后 Jessica 也尝试推送其变更，但是被服务器拒绝了。她被告知只有先执行获取及合并操作，此次所推送的非快进式变更才能生效。集中式工作流能够吸引大量用户的原因在于人们熟悉也很适应这样的范式。
这并不仅仅局限于小型团队。借助于 Git 的分支模型，上百名开发人员都可以同时通过多个分支顺利地在单个项目上工作。
## 5.1.2 集成管理者工作流

Git 允许用户拥有多个远程仓库，因此就存在这样一种工作流：每个开发人员对其公开仓库都具有写权限，对他人的仓库具有读权限。这种情形下通常还会包括一个代表官方项目的权威仓库（canonical repository）。要向该项目做贡献，你可以创建一份项目的公开克隆，将自己的修改推送上去。然后请求主项目的维护人员合并你的变更。维护人员可以将你的仓库添加为远程仓库，在本地测试变更，再将其合并入他们的分支并推送回权威仓库。该过程如下所示（见下图）。
（1）项目维护人员推送到公开仓库
（2）贡献者克隆该仓库，做出自己的修改
（3）贡献者推送到自己的公开仓库副本
（4）贡献者向维护人员发送电子邮件，要求合并变更
（5）维护人员将贡献者的仓库添加为远程仓库并在本地进行合并
（6）维护人员将合并后的变更推送到主仓库
![集成管理者工作流](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/%E9%9B%86%E6%88%90%E7%AE%A1%E7%90%86%E8%80%85%E5%B7%A5%E4%BD%9C%E6%B5%81.png)
这是诸如 Github 或 GitLab 这类中枢式工具最常用的工作流，可以轻而易举地派生出一个项目，然后将变更推送到其中，让所有人都能看到。这种方法的主要优势之一在于它不会耽误你的工作，主仓库的维护人员可以随时拉取你的变更。贡献者不用干等着项目合并自己的修改，大家都可以按照自己的步调各自行事。
## 5.1.3 司令官与副官工作流

这是多仓库工作流的一个变体。这种工作流通常是由涉及上百名协作人员的大型项目使用的。其中著名的一个例子就是 Linux 内核。被称为副官的各色集成管理者负责仓库的某一部分。所有的副官头上还有一位被称为司令官的集成管理者。司令官的仓库作为参考仓库（reference repository），供所有的协作人员从中拉取内容。该工作过程如下所示（见下图）。
（1）普通开发人员使用自己的主题分支，根据 master 分支进行变基。这里的 master 分支指的是司令官自己的
（2）副官将开发人员的主题分支合并入 master 分支
（3）司令官将副官的 master 分支合并入自己的 master 分支
（4）司令官将其 master 分支推送到参考仓库，同时其他开发人员以此为基础进行变基操作
![司令官与副官工作流](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/%E5%8F%B8%E4%BB%A4%E5%AE%98%E4%B8%8E%E5%89%AF%E5%AE%98%E5%B7%A5%E4%BD%9C%E6%B5%81.png)
这种工作流并不常见，但是在超大型项目或高度层次化环境中非常有用。项目负责人（司令官）可以籍此将大量工作委托出去，在整合之前再将各部分代码从四处收集回来。
## 5.1.4 工作流小结

在 Git 这类分布式系统中，有一些常用的工作流。但是在实际中，特定的工作流会出现很多不同的变化。现在你可以（希望能够）确定自己适合使用哪些工作流的组合，我们接下来会给出一些更为具体的例子，看看各种工作流中主要角色是如何操作的。在 5.2 节中，你将会学到为项目做贡献的一些常用模式。
# 5.2 为项目做贡献

描述如何为项目做贡献的主要困难在于实现这一目标的方法实在是太多了。由于 Git 极大的灵活性，人们协作的形式有很多，而且项目各不相同，因此没法去描述该如何做贡献。其中的不确定因素包括活跃的贡献者数量、采用的工作流、提交权限以及可能的外部贡献方法。
第一个不确定因素是活跃的贡献者数量，有多少用户为项目贡献了代码？贡献频率如何？在很多情况下，一天会有两三个开发人员提交几次，对于冷门项目来说，可能会很少。如果是大型公司或项目，开发人员的数量数以千计，每天会有成百上千次提交。认识到这一点很重要，因为随着参与的开发人员越来越多，你会碰到更多关于如何确保代码能够干净地应用或是轻松合并之类的问题。你所提交的变更可能被视为过时的，也可能会在你工作期间或是在等待变更被批准或应用的时候被合并入的内容严重破坏掉。你又该如何保持代码时刻处于最新状态？如何保证提交始终有效？
另一个不确定因素是项目采用的工作流。是否是集中式的，每名开发人员是否都对主线代码拥有相同的写权限？项目是否有维护人员或集成管理人员负责检查所有的补丁？所有的补丁是否都经过了同行评审并得到了批准？你是否参与了这个过程？有没有设立副官系统？你是否必须先把工作内容提交给他们？
接下来的问题是提交权限。在向项目做贡献时，是否具有项目的写权限决定了所采用的工作流也是很不一样的。如果没有写权限，项目该怎样接受贡献？是不是还得制定一套策略？你一次能够贡献多少？多久贡献一次？
所有这些问题都会影响到如何高效地为项目做贡献以及倾向选择或者能够选择的工作流。我们将由浅入深，采用一系列用例来分析其中每一个方面。通过这些例子，你应该能够建立起在实践中所需要的工作流。
## 5.2.1 提交准则

在查看特定的用例之前，有一个关于提交消息的注意事项。制定一份良好的提交准则并坚持贯彻能够很大程度上简化 Git 的使用以及与他人的协作。Git 项目提供了一份文档（Git 源代码中的 Documentation/SubmittingPatches 文件），其中对如何创建提交补丁提出了一些不错的建议。
首先，不要出现与空白字符相关的错误。Git 提供了一种简单方法来检查这些问题，即在提交之前执行 git diff --check，该命令能够鉴别并列出可能存在的空白字符错误。
如果在提交前执行这条命令，就可以知道提交中是否存在令其他开发人员烦心的空白字符问题。
接下来，尽量使每次提交在逻辑上都是一个独立的变更集。如果可以，变更的时候别贪多，不要花上整个周末去解决 5 个不同的问题，然后在周一的时候把它们递交成一个大块头的提交。即便是你不想在周末提交，也可以在周一的时候利用暂存区将先前的工作结果按照每个问题至少一次提交进行切分，并在提交中加入有用的信息。如果某些变更修改了同一个文件，尝试使用 git add --patch 来部分地暂存文件（在 7.2 节中会进行详细介绍）。不管你提交了 1 次还是 5 次，只要所有的变更最终都被添加，分支顶端的项目快照就不会有什么两样。考虑到你的同事还得审查你做出的的变更，尽量让事情简单点吧。如果随后需要拉出或还原变更集，这样子也能够更容易些。7.6 节描述了不少用于重写历史以及交互式暂存文件的 Git 技巧，这些工具能够在把工作结果发送给别人之前帮助你生成一份既整洁又易于理解的历史记录。
要记住的最后一件事就是提交消息。养成创建高质量提交消息的习惯能够简化 Git 的使用以及协作。作为一条普适性规则，提交消息的第一行不应该超过 50 个字符，它应该准确地描述变更集，紧跟着是一个空行，然后是更详细的解释。Git 项目要求在详细解释中还要包括做出变更的动机以及与先前实现之间的对比，这是一条值得遵循的良好准则。在消息中使用现在时态的祈使语气也是个不错的做法。换句话说，就是使用命令。不要用 I added tests for 或 Adding tests for，而是要用 Add tests for。下面是一个最初由 Tim Pope 编写的模板。

简要的变更汇总信息（不超过 50 个字符）

如果有必要，请附上更详尽的说明。请将
每行长度限制在 72 个字符左右。在某些情
况下，第一行会被作为邮件的主题，余下
的作为邮件正文。两者之间一定要使用空
行分隔（除非你不打算要正文部分）。如
果将两者写在一起，那么像 rebase 这样的
工具就不知道该如何处理了、

将余下的描述性嘻嘻你写在空行之后。

`- 可以使用这样的条目符号`
`- 通常选用连字符或星号作为条目符号
`  前面加上单个空格。条目之间用空行
`  分隔。不过这里没有固定的约定，可
`  以视情况改动。`

如果你所有的提交消息都是这样，于己于人都大有益处。Git 项目的提交消息采用了良好的格式，执行 git log --no-merges 就可以看到漂亮的格式化项目提交历史记录是什么样子的。
在接下来的例子以及本书的大部分内容中，出于简洁性的考虑，我们并没有采用这种美观的提交消息，而是使用了 git commit 命令的 -m 选项。可别学书中的样子，要按照我们说过的那样做。
## 5.2.2 私有小型团队

你可能碰到的最简单的配置就是一个私有项目加上一两名开发人员。在这里，私有的意思就是闭源，即外部世界无法访问。你和其他开发人员都有仓库的推送权限。
在这种环境中，你可以采用 Subversion 或其他集中式系统中所使用的工作流，仍然享受诸如离线提交、各种更简单的分支及合并操作等功能。主要的不同在于当提交时=，合并是发生在客户端而非服务器端。让我们来看看当两名开发人员在一个共享仓库上一起工作时会怎么样吧。第一个开发人员 John 克隆仓库，做出改动，然后在本地提交（为了减少这些例子所占用的篇幅，协议信息均被替换为...）。
```shell
# John's Machine
git clone john@githost:simplegit.git
Cloning into 'simplegit'...
...
cd simplegit/
vim lib/simplegit.rb
git commit -am 'removed invalid default value'
[master 738ee87] removed invalid default value
 1 files changed, 1 insertions(+), 1 deletions(-)
```
第二个开发人员 Jessica 也进行了同样的操作，克隆仓库并提交变更，如下所示。
```shell
# Jessica's Machine
git clone jessica@githost:simplegit.git
Cloning into 'simplegit'...
...
cd simplegit/
vim TODO
git commit -am 'add reset task'
[master fbff5bc] add reset task
 1 files changed, 1 insertions(+), 0 deletions(-)
```
现在，Jessica 向服务器推送其工作内容，如下所示。
```shell
# Jessica's Machine
git push origin master
...
To jessica@githost:simplegit.git
	1edee6b...fbff5bc  master -> master
```
John 也开始推送变更，如下所示。
```shell
# John's Machine
git push origin master
To john@githost:simplegit.git
 ! [rejected]  master -> master (non-fast forward)
error: failed to push some refs to 'john@githost:simplegit.git'
```
John 的推送操作被拒绝了，因为这时候 Jessica 已经完成了推送。如果你用惯了 Subversion，理解这一点非常重要，因为你会发现两名开发人员编辑的并不是同一个文件。如果编辑的文件不相同，Subversion 会在服务器上自动进行合并，但是在 Git 中，你必须在本地合并提交。John 需要获取 Jessica 的变更并进行合并，然后才能被允许提交，如下所示。
```shell
git fetch origin
...
From john@githost:simplegit
 + 049d078...fbff5bc master -> origin/master
```
这时候，John 的本地仓库看起来如下所示。
![John的分叉历史](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/John%E7%9A%84%E5%88%86%E5%8F%89%E5%8E%86%E5%8F%B2.png)
John 有一个引用，指向的是 Jessica 所推送的变更，但是在他能够推送之前，必须将其合并入自己的工作内容中，如下所示。
```shell
git merge origin/master
Merge made bu recursive.

TODO | 1+
1 files changed, 1 insertions(+), 0 deletions(-)
```
合并过程非常顺利，John 的提交历史现在看起来如下所示。
![合并了origin/master之后的John的仓库](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/%E5%90%88%E5%B9%B6%E4%BA%86originmaster%E4%B9%8B%E5%90%8E%E7%9A%84John%E7%9A%84%E4%BB%93%E5%BA%93.png)
现在，John 可以测试代码是否工作正常，然后就能够将新合并好的工作推送到服务器上了，如下所示。
```shell
git push origin master
...
To john@githost:simplegit.git
 fbff5bc...72bbc59 master -> master
```
John 最终的提交历史如下所示。
![John在推送到origin服务器之后的历史记录](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/John%E5%9C%A8%E6%8E%A8%E9%80%81%E5%88%B0origin%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B9%8B%E5%90%8E%E7%9A%84%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95.png)
在此期间，Jessica 已经开始在主题分支上工作了。她创建了一个叫作 issue64 的主题分支并在该分支上进行了 3 次提交。不过她还没有获取到 John 的变更，因此其提交历史看起来如下所示。
![Jessica的主题分支](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/Jessica%E7%9A%84%E4%B8%BB%E9%A2%98%E5%88%86%E6%94%AF.png)
Jessica 希望与 John 取得同步，因此她执行了获取操作，如下所示。
```shell
# Jessica's Machine
git fetch origin
...
From jessica@githost:simplegit
 fbff5bc..72bbc59 master -> origin/master
```
![Jessica在获取到John的变更之后的历史记录](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/Jessica%E5%9C%A8%E8%8E%B7%E5%8F%96%E5%88%B0John%E7%9A%84%E5%8F%98%E6%9B%B4%E4%B9%8B%E5%90%8E%E7%9A%84%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95.png)
Jessica 认为自己的主题分支已经没什么问题了，但是她想知道需要合并什么内容才能够进行推送。可以执行 git log 来找到答案，如下所示。
```shell
git log --no-merges issue54..origin/master
commit 738ee872852dfaa9d6634e0dea7a324040193016
Author: John Smith <jsmith@example.com>
Date: Fri May 29 16:01:27 2009 -0700

	removed invalid default value
```
issue54...origin/master 这种语法是一个日志过滤器，它要求 Git 显示出一个提交列表，该列表中的提交出现在后一个分支（在本例中是 origin/master）中，但不存在于前一个分支（在本例中是 issue54）中。我们之后会在 7.1.6 节中详细讲解该语法。
现在我们可以从输出中看出，只有一个由 John 所作出的提交是 Jessica 尚未合并的。如果她选择合并 origin/master，那么这个提交将会修改其本地工作内容。
Jessica 现在就可以将自己的主题分支以及 John 的工作（origin/master）合并入 master 分支，然后推送回服务器。首先，她要切换回自己的 master 分支来完成所有这些操作，如下所示。
```shell
git checkout master
Switched to branch 'master'
Your branch is behind 'origin/master' by 2 commits, and can be fast-forwarded
```
先合并 origin/master 或是 issue54 都可以，两者都属于上游，因此先后顺序并不重要。不管选择什么样的合并次序，最终的快照都是一样的，只有历史记录会略有不同。Jessica 选择先合并 issue54，如下所示。
```shell
git merge issue54
Updating fbff5bc..4af4298
Fast forward
 README            |      1 +
 lib/simplegit.rb  |      6 +++++-
 2 files changed, 6 insertions(+), 1 deletions(-)
```
一切正常。如你所见，这就是一个简单的快进式合并。现在要合并 John 的工作了（origin/master），如下所示。
```shell
git merge origin/master
Auto-merging lib/simplegit.rb
Merge made by recursive.
	lib/simplegit.rb |  2 +-
	1 files changed, 1 insertions(+), 1 deletions(-)
```
所有文件的合并都干干净净，Jessica 现在的历史记录看起来如下所示。
![Jessica合并了John的变更之后的历史记录](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/Jessica%E5%90%88%E5%B9%B6%E4%BA%86John%E7%9A%84%E5%8F%98%E6%9B%B4%E4%B9%8B%E5%90%8E%E7%9A%84%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95.png)
现在，Jessica 可以从自己的 master 分支访问 origin/master，因此也就能够顺利地推送了（假设 John 在此期间没有再次推送），如下所示。
```shell
git push origin master
...
To jessica@githost:simplegit.git
 72bbc59..8059c15 master -> master
```
每位开发人员都提交了几次并顺利地合并了他人的工作结果。
![Jessica将所有的变更推送回服务器之后的历史记录](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/Jessica%E5%B0%86%E6%89%80%E6%9C%89%E7%9A%84%E5%8F%98%E6%9B%B4%E6%8E%A8%E9%80%81%E5%9B%9E%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B9%8B%E5%90%8E%E7%9A%84%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95.png)
这是最简单的一种工作流。你工作一段时间后（通常是在主题分支上），在能够整合的时候合并到 master 分支。如果想共享工作结果，可以将其合并到你自己的 master 分支，要是有改动，获取并合并到 origin/master，最后再推送到服务器上的 master 分支。这个过程通常如下所示。
![一个简单的多开发人员Git工作流的事件顺序](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84%E5%A4%9A%E5%BC%80%E5%8F%91%E4%BA%BA%E5%91%98Git%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%9A%84%E4%BA%8B%E4%BB%B6%E9%A1%BA%E5%BA%8F.png)
## 5.2.3 私有管理团队

在接下来的场景中，你会看到在大型的私有团队中贡献者所扮演的角色。你将学到如何在特定的环境下工作：小组基于特性展开协作，然后由其他人来整合这些由团队所完成的贡献成果。
假设 John 和 Jessica 共同开发某个软件，同时 Jessica 和 Josie 共同开发另一个特性。在这种情况下，公司采用了一种集成管理者工作流，其中小组的工作只能由特定的工程师进行集成，主仓库的 master 分支也只能够由这些人来更新。在这种情况下，所有的工作流都是在基于团队的分支上完成的，随后由集成人员拉取到一起。
让我们来看看 Jessica 的工作流，她负责处理两个特性，同时与两名开发人员协作。假设她已经克隆了仓库，决定先处理 featureA。她为该特性创建了一个新的分支并做了一些工作，如下所示。
```shell
# Jessica's Machine
git checkout -b featureA
Switched to a new branch 'featureA'
vim lib/simplegit.rb
git commit -am 'add limit to log function'
[featureA 3300904] add limit to log function
 1 files changed, 1 insertions(+), 1 deletions(-)
```
这时，她需要与 John 共享工作内容，于是她将自己在 featureA 分支上的提交推送到了服务器。Jessica 并没有 master 分支的推送权限，只有集成人员才有，为了能与 John 协作，她只能推送到另一个分支，如下所示。
```shell
git push -u origin featureA
...
To jessica@githost:simplegit.git
 * [new branch] featureA -> featureA
```
Jessica 向 John 发送了电子邮件，告知自己已经向 featureA 分支推送了一些工作内容，他现在就可以查看了。在等待 John 回应的同时，Jessica 与 Josie 在 featureB 上也展示了工作。她一开始先基于服务器的 master 分支创建了一个新的特性分支，如下所示。
```shell
# Jessica's Machine
git fetch origin
git checkout -b featureB origin/master
Switched to a new branch 'featureB'
```
现在，Jessica 在 featureB 分支上完成了几次提交，如下所示。
```shell
vim lib/simplegit.rb
git commit -am 'made the ls-tree function recursive'
[featureB e5b0fdc] made the ls-tree function recursive
	1 files changed, 1 insertions(+), 1 deletions(-)
vim lib/simplegit.rb
git commit -am 'add ls-files'
[featureB 8512791] add ls-files
 1 files changed, 5 insertions(+), 0 deletions(-)
```
![Jessica的初始提交历史](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/Jessica%E7%9A%84%E5%88%9D%E5%A7%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2.png)
在准备推送工作内容的时候，她收到了 Josie 的电子邮件，邮件中说包含了一些先期工作的分支已经作为 featureBee 被推送到了服务器上。Jessica 在推送之前必须首先将这些变更与自己的进行合并。她可以利用 git fetch 获取到 Josie 的变更，如下所示。
```shell
git fetch origin
...
From jessica@githost:simplegit
 * [new branch]  featureBee -> origin/featureBee
```
Jessica 现在就可以使用 git merge 将其合并到自己的工作中了，如下所示。
```shell
git merge origin/featureBee
Auto-merging lib/simplegit.rb
Merge made by recursive.
 lib/simplegit.rb |  4 ++++
 1 files changed, 4 insertions(+), 0 deletions(-)
```
这有一点小问题：她需要将 featureB 分支中合并过的工作内容推送到服务器中的 featureBee 分支。这可以通过在 git push 命令后跟上一个冒号（:），然后再跟上远程分支来指定，如下所示。
```shell
git push -u origin featureB:featureBee
...
To jessica@githost:simplegit.git
 fba9af8..cd685d1 featureB -> featureBee
```
这叫做引用规格（refspec）。10.5 节会对此及其功能展开更详细的讨论。另外也要注意 -u 选项，它是 --set-upstream 的缩写，该选项能够配置分支以简化随后的推送与拉取。
接下来，John 发邮件给 Jessica，告知他已经向 featureA 推送了一些变更，要求 Jessica 进行验证。Jessica 执行 git fetch 来拉取这些变更，如下所示。
```shell
git fetch origin
...
From jessica@githost:simplegit
 3300904..aad881d featureA -> origin/featureA
```
然后使用 git log 查看变更的具体内容，如下所示。
```shell
git log featureA..origin/featureA
commit aad881d154acdaeb2b6b18ea0e827ed8a6d671e6
Author: John Smith <jsmith@example.com>
Date: Fri May 29 19:57:33 2009 -0700

	changed log output to 30 from 25
```
最后，她将 John 的工作合并入自己的 featureA 分支，如下所示。
```shell
git checkout featureA
Switched to branch 'featureA'
git merge origin/featureA
Updating 3300904..aad881d
Fast forward
 lib/simplegit.rb | 10 +++++++++-
1 files changed, 9 insertions(+), 1 deletions(-)
```
Jessica 想要做一些微调，于是重新提交，然后再推送回服务器，如下所示。
```shell
git commit -am 'small tweak'
[featureA 774b3ed] small tweak
1 files changed, 1 insertions(+), 1 deletions(-)
git push
...
To jessica@githost:simplegit.git
 3300904..774b3ed featureA -> featureA
```
现在，Jessica 的提交历史如下图所示。
![Jessica在特性分支上完成提交之后的历史记录](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/Jessica%E5%9C%A8%E7%89%B9%E6%80%A7%E5%88%86%E6%94%AF%E4%B8%8A%E5%AE%8C%E6%88%90%E6%8F%90%E4%BA%A4%E4%B9%8B%E5%90%8E%E7%9A%84%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95.png)
Jessica、Josie 和 John 提醒集成人员，服务器上的 featureA 和 featureBee 已经可以合并入主线了。在并入主线之后，一次获取操作将会得到要给新的合并提交，使得历史记录如下图所示。
![Jessica在合并完两个主题分支之后的历史记录](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/Jessica%E5%9C%A8%E5%90%88%E5%B9%B6%E5%AE%8C%E4%B8%A4%E4%B8%AA%E4%B8%BB%E9%A2%98%E5%88%86%E6%94%AF%E4%B9%8B%E5%90%8E%E7%9A%84%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95.png)
正是这种能够让多个团队并行工作，随后再分别合并的能力使得很多开发小组转向了 Git。Git 的一个巨大的优势在于，项目中一些小的子团体可以通过远程分支展开协作，而不会对整个团队造成影响或妨碍。你在这里所看到的工作流顺序如下图所示。
![管理团队工作流的基本顺序](https://front-end-1257950569.cos.ap-guangzhou.myqcloud.com/%E7%B2%BE%E9%80%9AGit%EF%BC%88%E7%AC%AC2%E7%89%88%EF%BC%89/%E7%AC%AC5%E7%AB%A0%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F%20Git/%E7%AE%A1%E7%90%86%E5%9B%A2%E9%98%9F%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%9A%84%E5%9F%BA%E6%9C%AC%E9%A1%BA%E5%BA%8F.png)
## 5.2.4 派生的公开项目

为公开项目做贡献有点不同。因为你并没有直接更新项目分支的权限，所以只能通过其他方式将工作结果交给项目维护人员。第一个例子描述了在对派生提供了良好支持的 Git 主机上利用派生进行贡献。很多托管站点支持这种功能（包括 Github、BitBucket、Google Code、repo.or.cz 等），很多项目维护人员也喜欢这种贡献方式。5.2.5 节将会讨论那些偏好通过电子邮件接受补丁的项目。
首先，你得有一个主仓库的克隆，为你打开贡献的补丁创建一个主题分支在该分支上展开工作。这一系列操作如下所示。
```shell
git clone (url)
cd project
git checkout -b featureA
# (work)
git commit
# (work)
git commit
```
>注意
>你可能想使用 rebase -i 将工作内容压缩成单个提交，或是重新分配多个提交中的工作内容，以便维护人员更容易评审补丁。7.6 节会对交互式变基做更详尽的介绍。





































