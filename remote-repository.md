# 远程仓库

> Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。某台机器有一个原始版本库，别的机器可以“克隆”这个原始版本库，而且每台机器的版本库都是一样的，并没有主次之分。
>
> 实际情况往往是这样，找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。

## 添加远程库

首先在 Github 创建一个 Git 仓库：new ----> 填写 Repository Name ----> Create repository。

在此我们将通过本地库初始化远程仓库所以不创建任何文件。

创建完成后，Git给出了三种方式：

### 命令行创建本地库并推送

```
echo "# git-notes" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M master
git remote add origin https://github.com/merlotliu/git-notes.git
git push -u origin master
```

### 直接推送已存在的本地库

```
git remote add origin https://github.com/merlotliu/git-notes.git
git branch -M master
git push -u origin master
```

### 从其他仓库导入

You can initialize this repository with code from a Subversion, Mercurial, or TFS project.

### 示例

```shell
# origin 是為遠程庫指定的名字（習慣命名可以使用其他）
$ git remote add origin git@github.com:merlotliu/git-notes.git

$ git branch -M master

$ git push -u origin master
Enumerating objects: 41, done.
Counting objects: 100% (41/41), done.
Delta compression using up to 4 threads
Compressing objects: 100% (37/37), done.
Writing objects: 100% (41/41), 63.64 KiB | 1.72 MiB/s, done.
Total 41 (delta 15), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (15/15), done.
To github.com:merlotliu/git-notes.git
 * [new branch]      master -> master
branch 'master' set up to track 'origin/master'.

```

就這樣完成了本地庫和遠程庫的同步與關聯，後期可以繼續通過相關指令推送或拉取。

## 刪除远程库

先查看远程库的信息

```
$ git remote -v
origin  git@github.com:merlotliu/git-notes.git (fetch)
origin  git@github.com:merlotliu/git-notes.git (push)
```

然后，根据名字删除，比如删除`origin`：

```
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

## 克隆远程库

同時，在Github創建倉庫的時候，可以完成初始化的操作。

勾選 `Add a README file`即可。

![image-20220811122659443](C:\Users\Danie\merlotliu.github\merlotliu.notes\git-notes\remote-repository.assets\image-20220811122659443.png)

然後在本地使用`git clone <repo-name>`，克隆到本地即可。

```shell
$ git clone git@github.com:merlotliu/git-notes.git git-copy
Cloning into 'git-copy'...
remote: Enumerating objects: 60, done.
remote: Counting objects: 100% (60/60), done.
remote: Compressing objects: 100% (35/35), done.
remote: Total 60 (delta 22), reused 58 (delta 20), pack-reused 0
Receiving objects: 100% (60/60), 104.61 KiB | 196.00 KiB/s, done.
Resolving deltas: 100% (22/22), done.
```

後面的`git-copy`為本地倉庫的目錄名，如果不指定則爲倉庫的名字。

## Notes

Git支持多种协议，包括`https`，但`ssh`协议速度最快。默认的`git://`使用`ssh`。

## Reference

1. [远程仓库 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/896954117292416)
2. [添加远程库 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/898732864121440)
3. [从远程库克隆 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/898732792973664)