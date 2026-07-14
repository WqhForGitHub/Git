现在，你已经学会了管理或维护 Git 仓库，实现源码控制所需的大多数日常命令和工作流，圆满完成了跟踪和提交文件的基本任务，对于暂存区和轻量级主题分支及合并的威力也尽在掌握中。
接下来你将要领略一系列极其强大的 Git 功能，你未必每天都会用到这些功能，但是它们将来也许能派上用场。
# 7.1 选择修订版本

Git 允许你使用多种方法来指定某次或一定范围内的提交。了解它们并非必须，但是学习一下总没坏处。
## 7.1.1 单个修订版本

你显然可以通过给出的 SHA-1 散列值来引用某次提交，不过还有对用户更为友好的提交引用方法。本节阐述了引用单个提交的各种方法。
### 7.1.1.1 短格式的 SHA-1

Git 非常聪明，只要你输入了 SHA-1 值的前几个字符，它就知道你想使用哪次提交，前提是这部分字符不少于 4 个且没有歧义，也就是说在当前仓库中有且只有一个对象的起始 SHA-1 与你输入的一样。
例如，要查看某次提交，假设你执行 git log 命令，并找出添加了特定功能的那次提交，如下所示。
```shell
git log
commit 734713bc047d87bf7eac9674765ae7933478c50d3
Author: Scott Chacon <schacon@gmail.com>
Date: Fri Jan 2 18:32:33 2009 -0800

	fixed ref handling, added gc auto, updated tests

commit d921970aadf03b3cf0e71becdaab3147ba71cdef
Merge: 1c002dd... 35cfb2b...
Author: Scott Chacon <schacon@gmail.com>
Date: Thu Dec 11 15:08:43 2008 -0800
	
	Merge commit 'phedders/rdocs'

commit 1c002dd4b536e7579fe34593e72e6c6c1819e53b
Author: Scott Chacon <schacon@gmail.com>
Date: Thu Dec 11 14:58:32 2008 -0800
	
	added some blame and merge stuff
```
在这个例子中，选择 1c002dd...。如果你想对这个提交使用 git show 命令，那么下列命令都是等效的（假设短格式没有出现歧义）。
```shell
git show 1c002dd4b536e7479fe34593e72e6c6c1819e53b
git show 1c002dd4b536e7479f
git show 1c002d
```


