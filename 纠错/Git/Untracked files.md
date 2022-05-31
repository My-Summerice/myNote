# Untracked files:

### 问题描述：

```
summerice@summerice-ubuntu:~/goWorkSpace/gopher$ git status
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        Network/
        go.mod
        go.sum

nothing added to commit but untracked files present (use "git add" to track)
```

### 解决办法：

那么解决方法就是先把本地缓存删除（改变成未被追踪状态），然后再提交：

```
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
git push -u origin master
```

注意：
 1、.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。

2、想要.gitignore起作用，必须要在这些文件不在暂存区中才可以，.gitignore文件只是忽略没有被staged(cached)文件， 对于已经被staged文件，加入ignore文件时一定要先从staged移除，才可以忽略。