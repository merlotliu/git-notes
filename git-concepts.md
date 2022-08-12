## 工作区 & 暂存区

Git和其他版本控制系统如SVN的一个不同之处就是有暂存区的概念。

### 工作区（Working Directory）

前文创建的Git目录中非隐藏的目录，都是工作区。

可以使用 `git status`查看當前狀態，如果工作區沒有任何修改或修改已提交完，則表明工作區是乾淨的。

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

### 版本库（Repository）

目录下的`.git`即为版本库。

版本库中的stage（或index）即为暂存区，此外在`git init`时，会默认创建默认的`master`分支，`HEAD`指针默认指向`master`分支。

![git-repo](.gitbook/assets/git-concepts.assets/0.jpeg)

`git add`的作用：将文件修改添加到暂存区。

`git commit`的作用：将暂存区的内容提交到当前分支（分支是可以切换的）。

事实上就是，将需要提交的文件添加到暂存区，然后将暂存区的内容一次提交到分支。

### 示例

1. 添加LICENSE新文件；

2. 对README.md修改为以下内容：

   ```
   Git is a distributed version control system.
   Git is free software distributed under the GPL.
   Git has a mutable index called stage.
   ```

3. 查看當前狀態：

   ```shell
   $ git status
   On branch master
   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git restore <file>..." to discard changes in working directory)
           modified:   README.md
   
   Untracked files:
     (use "git add <file>..." to include in what will be committed)
           LICENSE
   ```

​		显然，输出告诉我们README.md被修改，LICENSE从未被提交。

4. 将两个文件添加到暂存区：

   ```
   $ git add README.md LICENSE
   ```

   如下為當前版本庫狀態

   ![git-stage](.gitbook/assets/git-concepts.assets/0-16601869145852.jpeg)

5. 提交到分支

   ```shell
   $ git commit -m "understand how stage works"
   [master f1e7833] understand how stage works
    2 files changed, 1 insertion(+), 1 deletion(-)
    create mode 100644 LICENSE
   ```

   `git commit`相當於將暫存區清空了，然後版本庫狀態變更爲

   ![git-stage-after-commit](.gitbook/assets/git-concepts.assets/0-16601870208314.jpeg)

## 修改

Git 管理的是修改而非文件。

什麽是修改？創建文件、在文件中添加内容或刪除内容、刪除文件等都是修改。

### 理解修改

依次執行以下操作：

修改README.md ----> git add README.md ----> 再次修改README.md ----> git commit -m "message" ----> git status

如果管理的是文件，最後顯示的狀態下，工作區應該是乾净的。

而如果管理的是修改，第二次修改則沒有被提交。

### 撤銷修改

#### 文件修改後尚未添加到暫存區

1 首先使用`git status`查看當前狀態

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md
```

可以發現，Git提示，可以使用`git restore <file>...`丟棄工作區的修改。（值得注意的是，根據Git的版本不同，命令有所區別）

2 使用`git restore README.md` 丟棄工作區修改。

#### 文件修改後已添加到暫存區

1 首先使用`git status`查看當前狀態

```
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md
```

可以發現，Git提示，可以使用 `git restore --staged <file>...`將添加到暫存區的修改丟棄（unstage）。

2 使用`git restore --staged README.md` .`將添加到暫存區的修改丟棄。

3 使用`git restore README.md` 丟棄工作區修改。

#### 文件修改且提交到分支

如果沒有推送到远程库，可以通过版本回退丢弃修改。

### 删除文件

假定我们已经添加了`test.txt`文件到版本库

```
$ git add text.txt
$ git commit -m "add text.txt"
[master 7774404] add text.txt
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 text.txt
```

使用`rm test.txt`刪除了文件。此時仍然可以類似撤銷修改的方式恢復該文件，且此時尚未`commit`。

如果确定要删除文件，使用`git rm test.txt`或`git add test.txt`，添加到暂存区，然后`git commit`即可。

此外，对于确定要删除的文件，可以直接使用`git rm <file>`，这一命令相当于`rm <file>`与`git add <file>`的组合。

### Notes

1. 没有提交到版本库的修改是不被追踪的，也就是无法被恢复的；
2. `git rm <file>`与`git add <file>`一样都会将修改添加到暂存区。

## Reference

1. [工作区和暂存区 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/897271968352576)
1. [管理修改 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/897884457270432#0)
1. [撤销修改 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/897889638509536)
1. [删除文件 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/900002180232448#0)