





# git init



```bash
git init
```





# git clone



## 1. 克隆现有仓库

**`允许你指定克隆仓库的本地目录名称`**

```bash
git clone <repository_url> <directory_name>
```



## 2. 克隆指定分支

**`指定克隆的分支。默认情况下，git clone 会克隆远程仓库的默认分支（通常是 main 或 master）。`**

```bash
git clone --branch develop https://github.com/your-username/your-repository.git
```





# git status



## 1. 使用简短格式查看状态

```bash
git status -s
```







# git diff



## 1. 查看工作区中 README.md 文件的更改

```bash
git diff README.md
```







# git commit



## 1. 提交暂存的更改

```bash
git add .
git commit -m "添加了新的功能"
```



## 2. 提交所有已修改的文件

```bash
git commit -a -m "修改了样式和修复了 bug"
```



## 3. 修改最近一次提交的信息

```bash
git commit --amend -m "更正了提交信息"
```



## 4. 将新的更改添加到最近一次提交中

```bash
git add  new_file.txt
git commit --amend --no-edit
```





# git rm



## 1. 删除单个文件

```bash
git rm unwanted_file.txt
git commit -m "删除 unwanted_file.txt"
```





## 2. 强制删除已修改的文件

```bash
git rm -f modified_file.txt
git commit -m "强制删除 modified_file.txt"
```









# git branch



## 1. 查看分支：

* **`列出所有本地分支：`**

```bash
git branch
```

**`当前分支会以绿色高亮显示，并带有 * 标记。`**

* **`列出所有远程分支：`**

```bash
git branch -r
```

**`显示所有远程仓库的分支，例如 origin/main。`**

* **`列出所有本地和远程分支：`**

```bash
git branch -a
```

**`显示所有本地和远程仓库的分支。`**



## 2. 创建分支

* **`创建新分支：`**

```bash
git branch <branch_name>
```

**`例如：git branch feature/new-feature 创建一个名为 feature/new-feature 的新分支。注意：这只是创建了分支，并没有切换到该分支。`**

* **`创建并切换到新分支：`**

```bash
git checkout -b <branch_name>
```

**`例如：git checkout -b feature/new-feature 创建一个名为 feature/new-feature 的新分支，并立即切换到该分支。也可以使用 git switch -c <branch_name> 达到同样的效果。`**



## 3. 切换分支

* **`切换到已存在的分支：`**

```bash
git checkout <branch_name>
```

**`例如：git checkout main 切换到 main 分支。也可以使用 git switch <branch_name> 达到同样的效果。`**



## 4. 重命名分支

* **`重命名当前分支：`**

```bash
git branch -m <new_branch_name>
```

**`例如：git branch -m feature/updated-feature 将当前分支重命名为 feature/updated-feature。`**

* **`重命名指定分支：`**

```bash
git branch -m <old_branch_name> <new_branch_name>
```

**`例如：git branch -m feature/old-feature feature/new-feature 将 feature/old-feature 分支重命名为 feature/new-feature。注意：如果 <new_branch_name> 已经存在，需要使用 -M 强制重命名。`**





## 5. 删除分支

* **`删除本地分支（已合并）：`**

```bash
git branch -d <branch_name>
```

**`例如：git branch -d feature/completed-feature 删除 feature/completed-feature 分支，前提是该分支已经合并到当前分支。`**



* **`强制删除本地分支（未合并）：`**

```bash
git branch -D <branch_name>
```

**`例如：git branch -D feature/abandoned-feature 强制删除 feature/abandoned-feature 分支，即使该分支尚未合并。警告：强制删除会丢失未合并的更改，请谨慎使用。`**



* **`删除远程分支：`**

```bash
git push origin --delete <branch_name>
```

**`例如：git push origin --delete feature/remote-feature 删除远程仓库 origin 上的 feature/remote-feature 分支。也可以使用 git push origin :<branch_name> 达到同样的效果。`**

