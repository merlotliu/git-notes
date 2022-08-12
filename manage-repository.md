# 版本庫管理

## 修改README.md文件

修改爲以下内容：

```
Git is a distributed version control system.
Git is free software.
```

## git status

运行`git status`命令，查看當前狀態：

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        create-repository.md
        git-troubleshooting.md
        manage-repository.md
        
no changes added to commit (use "git add" and/or "git commit -a")
```

可以發現，輸出信息告訴我們，`README.md`文件被修改了但沒有提交。

未被添加的文件状态为`Untracked`。

當前沒有`add`任何文件。

## git diff

用`git diff`命令查看工作區和版本庫内容的區別：

```
$ git diff
diff --git a/README.md b/README.md
index 4620b7e..c9b621f 100644
--- a/README.md
+++ b/README.md
@@ -1,2 +1,2 @@
-Git is a version contorl system.
+Git is a distributed version contorl system.
 Git is free software.
```

在命令行還能看出顏色的區別，紅色為版本庫的内容，綠色為工作區的内容，白色表示一致。

確定變更的内容沒有問題之後，可以繼續使用`git add`、`git commit`、`git status`提交或查看當前狀態。

```shell
$ git add README.md

$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   README.md

$ git commit -m "add distributed"
[master 2ffd4d4] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)

$ git status
On branch master
nothing to commit, working tree clean
```

## 小結

1. `git status`查看當前版本庫狀態；
2. 用`git diff`查看版本庫與工作區的區別。

## Reference

1. [时光机穿梭 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/896954074659008#0)