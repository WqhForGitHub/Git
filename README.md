# 假设要将本地和远程分支 master 重命名为 legacy

1. **`重命名本地分支：`**
```bash
git branch -m master legacy
```

2. **`删除远程分支`**
```bash
git push origin --delete master
```

3. **`推送新的本地分支并设置 upstream：`**
```bash
git push origin -u legacy
```
