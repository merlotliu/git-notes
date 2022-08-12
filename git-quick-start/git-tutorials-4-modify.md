# 管理修改

Git 管理的是修改而非文件。

什麽是修改？創建文件、在文件中添加内容或刪除内容、刪除文件等都是修改。

## 理解修改

依次執行以下操作：

修改README.md ----> git add README.md ----> 再次修改README.md ----> git commit -m "message" ----> git status

如果管理的是文件，最後顯示的狀態下，工作區應該是乾净的。

而如果管理的是修改，第二次修改則沒有被提交。

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

### 文件修改且提交到分支

如果沒有推送到远程库，可以通过版本回退丢弃修改。

## 删除文件

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