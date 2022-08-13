# 管理修改

Git 管理的是修改而非文件。

什麽是修改？創建文件、在文件中添加内容或刪除内容、刪除文件等都是修改。

## 理解修改

依次執行以下操作：

修改README.md ----> git add README.md ----> 再次修改README.md ----> git commit -m "message" ----> git status

如果管理的是文件，最後顯示的狀態下，工作區應該是乾净的。

而如果管理的是修改，第二次修改則沒有被提交。

```
# 查看当前README内容
$ cat README.md
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.

# 第一次修改
$ echo "Git tracks changes." >> README.md

# 添加到暂存区
$ git add README.md

# 查看状态
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md


# 第二次修改
$ vim README.md

# 查看修改内容
$ cat README.md
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.

# 提交暂存区内容
$ git commit -m "git tracks changes"
[master 0d136ba] git tracks changes
 1 file changed, 1 insertion(+)

# 查看当前状态，发现仍有修改未提交
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")

# 查看文件中修改未提交内容，发现第二次修改确实没提交
$ git diff HEAD -- README.md
diff --git a/README.md b/README.md
index 76d770f..a9c5755 100644
--- a/README.md
+++ b/README.md
@@ -1,4 +1,4 @@
 Git is a distributed version control system.
 Git is free software distributed under the GPL.
 Git has a mutable index called stage.
-Git tracks changes.
+Git tracks changes of files.

```

## 撤銷修改

### 文件修改後尚未添加到暫存區

#### 1 首先使用`git status`查看當前狀態

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md
```

可以發現，Git提示，可以使用`git restore <file>...`丟棄工作區的修改。（值得注意的是，根據Git的版本不同，命令有所區別）

#### 2 使用`git restore README.md` 丟棄工作區修改。

#### 命令行示例

```
# 做了一些错误的修改
$ echo "My stupid boss still prefers SVN." >> README.md
$ cat README.md
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
My stupid boss still prefers SVN.

# 查看一下当前状态，发现还没有添加到暂存区
# Git 提示可以使用 "git restore <file>..." 撤销工作去修改
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")

# 撤销工作区修改
$ git restore README.md
```

### 文件修改後已添加到暫存區

#### 1 首先使用`git status`查看當前狀態

```
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md
```

可以發現，Git提示，可以使用 `git restore --staged <file>...`將添加到暫存區的修改丟棄（unstage）。

#### 2 使用`git restore --staged README.md` .`將添加到暫存區的修改丟棄。

#### 3 使用`git restore README.md` 丟棄工作區修改。

#### 命令行示例

```
# 又一次添加错误内容，并添加到了暂存区
$ echo "My stupid boss still prefers SVN." >> README.md
$ cat README.md
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
My stupid boss still prefers SVN.
$ git add README.md

# Git 提示可以通过 "git restore --staged <file>..." 丢弃修改
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md

# 丢弃暂存区修改
$ git restore --staged README.md

# 现在的情况和没有添加到暂存区是一样的
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")

# 丢弃工作区域修改
$ git restore README.md

# 发现文件又回到了修改前的样子
$ cat README.md
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes.
```

### 文件修改且提交到分支

如果沒有推送到远程库，可以通过版本回退丢弃修改。

## 删除文件

假定我们已经添加了`test.txt`文件到版本库

```
# 创建文件并提交到分支
$ touch test.txt
$ git add test.txt
$ git commit -m "add test.txt"
[master 7454a99] add test.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.txt
```

使用`rm test.txt`刪除了文件。此時仍然可以類似撤銷修改的方式恢復該文件，且此時尚未`commit`。

```
$ rm test.txt

$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")

$ git restore test.txt

$ ls
LICENSE  README.md  test.txt
```

如果确定要删除文件，使用`git rm test.txt`或`git add test.txt`，添加到暂存区，然后`git commit`即可。

此外，对于确定要删除的文件，可以直接使用`git rm <file>`，这一命令相当于`rm <file>`与`git add <file>`的组合。

```
# 刪除工作區的文件
$ rm test.txt

$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")

# 会删除工作区和暂存区的文件，同时添加删除的修改到暂存区
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master c11fc99] remove test.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 delete mode 100644 test.txt
```

## Notes

1. `git restore --staged <file>`丢弃暂存区修改；
2. `git restore <file>`丢弃工作区修改；
3. 对于已经提交的内容，采取版本回退的方式；
4. 没有提交到版本库的修改是不被追踪的，也就是无法被恢复的；
5. `git rm <file>`与`git add <file>`一样都会将修改添加到暂存区。

## Reference

1. [管理修改 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/897884457270432#0)
1. [撤销修改 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/897889638509536)
1. [删除文件 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/900002180232448#0)