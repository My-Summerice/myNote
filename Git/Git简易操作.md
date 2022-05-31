### Git -- github

---

### 1.安装Git

```
sudo apt-get install git
```

### 2.选择一个目录进行初始化git

```
git init
```

### 3.添加文件到暂存区

```
git add [filename]
```

### 4.提交暂存区内容到本地仓库

```
git commit -m "自己对这次提交添加的注释"
```

- 注意：本质是本地仓库的快照指针指向了暂存区的地址。

### 5.推送本地仓库内容到远程仓库

```
// 添加远程仓库地址并在本地命名为origin
git remote add origin git@github.com:[你自己的仓库地址.git]	
// 将本分支的内容提交到远程仓库的某分支下，默认为master分支
git push -u origin [分支名]
```

### 6.下拉文件与拷贝文件

```
// 从远程仓库下载新分支与数据
git fetch
// 紧接着合并新下载的分支到自己所在的分支
git merge [被合并的分支名]

// 拷贝一份远程仓库到当前目录
git clone [url] [你想要的文件名]
```

### 7.查看仓库状态

```
// 查看当前工作区与本地仓库的差异
git status
// 查看暂存区和工作区文件的具体差异，文件名可选
git diff [文件名]
```

### 8.回退版本

```
// 回退所有内容到上n个版本
git reset HEAD^
git reset HEAD~n [filename]
```

### 9.分支管理

```
// 查看分支
git branch
// 创建分支
git branch [branchname]
// 切换分支
git checkout [branchname]
// 合并分支
git merge [branchname]
```

注意：

- 合并分支时若出现冲突则需要手动修改为你想要的内容
- 修改完成后记得git add，告诉Git文件冲突已解决
- 接着正常提交就好

### 10.查看历史提交记录

```
git log
```

### 11.操作工作区文件

```
// 从暂存区和工作区删除文件
git rm [filename]
// 强制删除
git rm -f [filename]
// 仅删除暂存区中的某文件
git rm --cached [filename]
```

注意：文件名可以使用通配符进行模糊匹配

```
// 移动或重命名一个文件、目录或软连接
git mv [file] [newfile]
// 同样的，添加[-f]可以强制重命名，即使newfile已经存在
```

