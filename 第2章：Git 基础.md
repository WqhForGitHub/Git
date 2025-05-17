### 2.1 获取 Git 仓库

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 

### 2.2 在 Git 仓库中记录变更

**1. 查看当前文件状态**

```powershell
$ git status
On branch master
nothing to commit, working directory clean
```

现在，让我们把一个简单的 README 文件添加到项目中。如果之前项目中不存在这个文件，那么这次执行 git status 就会看到这个未跟踪的文件：

```powershell
$ echo 'My Project' > README
$ git status
On branch master
Untracked files:
	(use "git add <file>..." to include in what will be committed)
	
		README
		
    nothing added to commit but untracked files present (use "git add" to track)
```



**2. 跟踪新文件**

可以使用 git add 命令让 Git 开始跟踪新的文件。执行以下命令来跟踪 README 文件：

```powershell
$ git add README
```

```powershell
$ git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new File:    README
```



**3. 暂存已修改的文件**

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

```                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       powershell
$ git add CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new file: README
	modified: CONTRIBUTING.md
```

```                                                                                                                                                                       powershell
$ vim CONTRIBUTING.md
$ git status
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

```                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               powershell
$ git add CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
	
	new file: README
	modified: CONTRIBUTING.md  
```



**4. 显示更简洁的状态信息**



**5. 忽略文件**

下面是另一个 .gitignore 文件的例子：

`*.a `                      # 忽略.a类型的文件

`!lib.a`                 # 仍然跟踪lib.a，即使上一行指令要忽略 .a 类型的文件

`/TODO `                 # 只忽略当前目录的TODO文件，而不忽略子目录下的TODO

`build/`                 # 忽略 build/ 目录下的所有文件

`doc/*.txt`           # 忽略doc/notes.txt，而不忽略 doc/server/arch.txt                                                                                                                                                                                                                                                                                                                                                 

`doc/**/*.pdf`     # 忽略 doc/目录下的所有 .pdf 文件 





### 2.6 标记

#### 1. 列举标签

在 Git 中，列举可用标签的操作很简单，只需键入 git tag 即可：

```                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     powershell
$ git tag
v0.1
v1.3
```

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   
