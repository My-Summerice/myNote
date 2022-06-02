# failed to push some refs to ''

### 问题描述：

```
summerice@summerice-ubuntu:~/MyNote$ git push
To github.com:My-Summerice/myNote.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'git@github.com:My-Summerice/myNote.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

### 解决办法：

- 分析出现该问题的原因是因为远程仓库的文件出现了改动，所以需要先将远程仓库合并到本地仓库，再进行push。

```
// 1.合并
summerice@summerice-ubuntu:~/MyNote$ git pull --rebase
remote: Enumerating objects: 8, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 6 (delta 2), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (6/6), done.
From github.com:My-Summerice/myNote
   0cb6b54..7e96a3f  master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: update
// 2.推送
summerice@summerice-ubuntu:~/MyNote$ git push
Counting objects: 5, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 551 bytes | 551.00 KiB/s, done.
Total 5 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:My-Summerice/myNote.git
   7e96a3f..097db1a  master -> master
```

- 成功！