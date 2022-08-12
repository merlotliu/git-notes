# 版本庫回退

首先，再修改一次README.md文件内容。

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

提交修改

```
$ git add README.md
$ git commit -m "append GPL"
[master 67c3c5e] append GPL
 1 file changed, 1 insertion(+), 1 deletion(-)
```

在項目開發的工程中，我們會不停的修改、添加、刪除文件，並一次次的提交，而有的時候我們的修改會導致項目的問題。這個時候我們就會想到回溯到上一個沒有問題的版本。我們每一次的commit就是一個版本，我們只需要找到一個正常的commit版本即可繼續我們的工作。

## git log

使用`git log`可以幫助我們查看commit版本信息：

```
$ git log
commit 67c3c5ed881638d0d4b43cd6ba05a14fd6651b37 (HEAD -> master)
Author: merlotliu <meetlouisliu@163.com>
Date:   Wed Aug 10 17:46:50 2022 +0800

    append GPL

commit 0f9a985c9d2f638e87451e3dba5dd0e43275c530
Author: merlotliu <meetlouisliu@163.com>
Date:   Wed Aug 10 17:45:10 2022 +0800

    modified manage-repository

commit c29906cff84fb639682861d871526b50dd48d14f
Author: merlotliu <meetlouisliu@163.com>
Date:   Wed Aug 10 17:39:51 2022 +0800

    add 3 files

commit 2ffd4d4a5c2905e1ad55871bc78ef91adea55e35
Author: merlotliu <meetlouisliu@163.com>
Date:   Wed Aug 10 17:38:01 2022 +0800

    add distributed

commit 8fa9ef54626d0b64e95b6701b25d7d6e75372757
Author: merlotliu <meetlouisliu@163.com>
Date:   Wed Aug 10 17:16:41 2022 +0800

    wrote a readme file
```

`git log`按照從最進到最遠的時間，顯示了我們提交的5個版本、相關提交説明以及作者等信息。

同時，可以添加相關參數以獲得更簡潔的日志信息，比如`--pretty=oneline`：

```
$ git log --pretty=oneline
67c3c5ed881638d0d4b43cd6ba05a14fd6651b37 (HEAD -> master) append GPL
0f9a985c9d2f638e87451e3dba5dd0e43275c530 modified manage-repository
c29906cff84fb639682861d871526b50dd48d14f add 3 files
2ffd4d4a5c2905e1ad55871bc78ef91adea55e35 add distributed
8fa9ef54626d0b64e95b6701b25d7d6e75372757 wrote a readme file
```

## Git GUI

可以從GUI中明顯的看到各個版本及其相關信息。

![image-20220810180047496](.gitbook/assets/backtrack-repository.assets/image-20220810180047496.png)

## git reset

在確定需要回退的版本之後，使用`git reset`命令幫助回退。在Git中，`HEAD`表示當前版本，`HEAD^`表示上一個版本。儅距離當前版本較遠時，可以使用數字，比如往上10個版本可寫成`HEAD~10`。

現在，我們將回退到上一版本。首先查看一下當前版本的README.md内容:

```
$ cat README.md
Git is a distributed version contorl system.
Git is free software distributed under the GPL.
Git Backtrack
```

“Git Backtrack”内容是我們在`append GPL`后又添加的新内容。

然後使用`git reset --hard <version>`開始回退：

```
$ git reset --hard HEAD^
HEAD is now at 67c3c5e append GPL
```

接著查看當前版本的`README.md`：

```
$ cat README.md
Git is a distributed version contorl system.
Git is free software distributed under the GPL.
```

顯然，我們成功了。

但此時再使用`git log`查看版本庫狀態卻發現最新的版本看不到了:

```
$ git log --pretty=oneline
67c3c5ed881638d0d4b43cd6ba05a14fd6651b37 (HEAD -> master) append GPL
0f9a985c9d2f638e87451e3dba5dd0e43275c530 modified manage-repository
c29906cff84fb639682861d871526b50dd48d14f add 3 files
2ffd4d4a5c2905e1ad55871bc78ef91adea55e35 add distributed
8fa9ef54626d0b64e95b6701b25d7d6e75372757 wrote a readme file
```

此時，如果我們想回到那個版本的狀態需要提供其對應的`commit id`，然後使用`git reset --hard <commit id>`：

```
$ git reset --hard c7261
HEAD is now at c7261c2 add backtrack-repository.md
```

顯然，`commit id`也並不需要寫全，保證前綴唯一即可。然後發現，我們又回到最新的版本。

## git reflog

如果忘記了`commit id`，可以使用 `git reflog`命令查看，該命令記錄了每一次的命令。

```
$ git reflog
67c3c5e (HEAD -> master) HEAD@{0}: reset: moving to HEAD^
c7261c2 HEAD@{1}: commit: add backtrack-repository.md
67c3c5e (HEAD -> master) HEAD@{2}: commit: append GPL
0f9a985 HEAD@{3}: commit: modified manage-repository
c29906c HEAD@{4}: commit: add 3 files
2ffd4d4 HEAD@{5}: commit: add distributed
8fa9ef5 HEAD@{6}: commit (initial): wrote a readme file
```

## 小結

1. `git log`查看提交歷史，即各版本信息；
2. `git reset --hard <commit id>`幫助版本回退，`<commit id>`可以是具體的版本號，也可以是`HEAD^`或`HEAD~num`；
3. `git reflog`查看命令歷史；

## Reference

1. [版本回退 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)