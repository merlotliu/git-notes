# Git 分支

## 创建与合并分支

### 原理

版本库的追踪就是`commit`的连续节点，`master`指向最新的`commit`，`HEAD`指向`master`。`master`仅为标识分支的指针，`HEAD`指向谁表示谁是当前分支。当只有`master`时，版本库的追踪如下：

![git-br-initial](../.gitbook/assets/git-branch.assets/0-16601950224412.png)

### 创建&切换分支

如果此时创建新分支`dev`，则创建一个指针`dev`指向`master`指向的`commit`。如果切换分支到`dev`，则：

![git-br-create](../.gitbook/assets/git-branch.assets/l.png)

因为仅仅创建指针和改变`HEAD`的指向，所以速度非常快。

### 修改并提交分支

切换分支后，我们进行修改并提交。此时，`dev`分支开始生长，而`master`不变，即：

![git-br-dev-fd](../.gitbook/assets/git-branch.assets/l-16601952313815.png)

### 合并分支

因为`master`此时并没有发生任何变更，所以将`dev`的内容合并到`master`仅需，移动`master`指针到`dev`的位置即可。

![git-br-ff-merge](../.gitbook/assets/git-branch.assets/0-16601953593367.png)

随后，我们就可以将`dev`分支删除。

### 示例

#### 1 创建分支切换分支：

```shell
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

或使用一条指令：

```
$ git checkout -b dev
Switched to a new branch 'dev'
```

#### 2 查看當前分支：

```
$ git branch
* dev
  master
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

#### 3 修改`dev`分支内容並提交；

```
$ echo "Creating a new branch is quick." >> README.md
$ git add README.md
$ git commit  -m "branch test"
[dev 882e9c3] branch test
 1 file changed, 1 insertion(+)
```

#### 4 切換回`master`分支：

```
$ git checkout master
Switched to branch 'master'
```

此時，多個commit、master、dev和HEAD形成的DAG（有向無環圖）如下：

![git-br-on-master](../.gitbook/assets/git-branch.assets/0.png)

#### 5 將`dev`分支的工作成果合并到`master`分支上：

```
$ git merge dev
Updating 2296a12..882e9c3
Fast-forward
 README.md | 1 +
 1 file changed, 1 insertion(+)
```

注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。因为`master`分支没有任何更改，即两个分支没有出现冲突，`dev`为最新的内容，仅需要移动`master`指针即可。

#### 6 删除`dev`分支：

```
$ git branch -d dev
Deleted branch dev (was 882e9c3).
```

删除后，查看`branch`，就只剩下`master`分支了：

```
$ git branch
* master
```

### git switch

`git checkout`提供了多种功能，但用来切换分支总觉得不太好理解。所以Git提供了新的git switch命令来切换分支：

创建并切换到新的`dev`分支，可以使用：

```
$ git switch -c dev
```

直接切换到已有的`master`分支，可以使用：

```
$ git switch master
```

使用新的`git switch`命令，比`git checkout`要更容易理解。

### Notes

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。

### 小结

Git鼓励大量使用分支：

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`或者`git switch <name>`

创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

## 合并冲突

在项目开发过程中，由于有多条分支且在一段时间内均为单独开发，这样就有可能在不同分支对同一文件进行了修改，从而产生冲突。

### 示例

考虑以下情况：

目前，当前仓库仅有`master`分支，接着创建`feature`分支。

然后分别对两个分支的`README.md`修改。在`feature`分支中，追加`Creating a new branch is quick AND simple.`到`README.md`。切换回`master`分支，在`master`分支中，追加`Creating a new branch is quick & simple.`到`README.md`。

```
$ git switch -c feature
Switched to a new branch 'feature'

$ echo "Creating a new branch is quick AND simple."  >> README.md

$ git add README.md
$ git commit -m "AND simple"
[feature 03b654f] AND simple
 1 file changed, 2 insertions(+)
 
 $ git reset --hard HEAD^
HEAD is now at 882e9c3 branch test

$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

$ echo "Creating a new branch is quick & simple." >> README.md

$ git add README.md
$ git commit -m "& simple"
[master c72882e] & simple
 1 file changed, 1 insertion(+)
```

准备将`feature`内容合并到`master`。

```shell
$ git merge feature
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

此时，由于`README.md`内容冲突，将无法进行快进，需要先解决冲突。当前的版本库节点图如下：

![git-br-feature1](../.gitbook/assets/git-branch.assets/merge-conflict-1.png)

### 解決

1. 查看當前狀態，再次确定冲突

   ```shell
   $ git status
   On branch master
   Your branch is ahead of 'origin/master' by 2 commits.
     (use "git push" to publish your local commits)
   
   You have unmerged paths.
     (fix conflicts and run "git commit")
     (use "git merge --abort" to abort the merge)
   
   Unmerged paths:
     (use "git add <file>..." to mark resolution)
           both modified:   README.md
   
   no changes added to commit (use "git add" and/or "git commit -a")
   ```

    (fix conflicts and run "git commit")这就是Git的解决建议。

2. 打開衝突文件，修改爲最終需要的内容，然後提交。

   ```
   Git is a distributed version control system.
   Git is free software distributed under the GPL.
   Git has a mutable index called stage.
   Git tracks changes.
   Creating a new branch is quick.
   <<<<<<< HEAD
   Creating a new branch is quick & simple.
   =======
   
   Creating a new branch is quick AND simple.
   >>>>>>> feature
   ```

   Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容，我们修改如下后保存：

   ```
   Creating a new branch is quick and simple.
   ```

   再提交：

   ```
   $ git add README.md
   $ git commit -m "confict fixed in README.md"
   [master 228887d] confict fixed in README.md
   ```

3. 查看分支合并情況（其中的`*`代表的是就是`commit`节点）

   ```
   $ git log --graph --pretty=oneline --abbrev-commit
   *   228887d (HEAD -> master) confict fixed in README.md
   |\
   | * 03b654f (feature) AND simple
   * | c72882e & simple
   |/
   * 882e9c3 (dev) branch test
   * 2296a12 (origin/master) add test.txt
   * c11fc99 remove test.txt
   * 7454a99 add test.txt
   * 0d136ba git tracks changes
   * 4075e3b understand how stage works
   * 489bb6c append GPL
   * 2d80ce0 add distributed
   * 954d5a7 wrote a readme file
   ```

   當前各分支的狀態圖：

   ![git-br-conflict-merged](../.gitbook/assets/git-branch.assets/merge-conflict-2.png)

4. 刪除已經完成工作的`feature`分支。

   ```
   $ git branch -d feature
   Deleted branch feature (was 03b654f).
   ```

### 小結

合并出现冲突时，Git会提醒我们消除冲突，根据提示修改相应文件并提交，即可完成冲突合并。

用`git log --graph`命令可以看到分支合并图。（其中的`*`代表的是就是`commit`节点）

## 分支管理策略

合并分支时，如果其他分支创建之后，合并到的分支没有提交修改，Git会采用 `Fast forward` 模式。在这一模式下，分支删除后，不能看出任何合并信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

### 示例

1. 创建新分支`dev`；
2. 修改README.md，提交修改到`dev`；
3. 切换回`master`。使用`git merge`合并分支，并带上`--no-ff`选项禁用`Fast forward`模式，后面跟上`-m "<message>"`，最后加上需要合并的分支（dev）。
4. 最后可以使用`git log --graph --pretty=oneline --abbrev-commit`查看分支合并图。

```shell
$ git switch -c dev
Switched to a new branch 'dev'

$ cat README.md
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes.
Creating a new branch is quick and simple.

$ echo "Merge branch without fast forward." >> README.md

$ git add README.md

$ git commit -m "add merge"
[dev 2bc5f94] add merge
 1 file changed, 1 insertion(+)

$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 4 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merge without no-ff" dev
Merge made by the 'ort' strategy.
 README.md | 1 +
 1 file changed, 1 insertion(+)

$ git log --graph --pretty=oneline --abbrev-commit
*   b72bb0f (HEAD -> master) merge without no-ff
|\
| * 2bc5f94 (dev) add merge
|/
*   228887d confict fixed in README.md
```

整个节点图如下：

![policy-branch-1](../.gitbook/assets/git-branch.assets/policy-branch-1.png)

### 分支策略

1. `master`应作为稳定的发布分支，尽量避免修改；
2. `dev`为开发分支，平时的小修小补都在上面（`dev`应该是最新的内容）；
3. 每次工作的合理步骤：
   1. 拉取（pull）`dev`的最新内容，以此为基础创建新分支；
   2. 在新分支开发，开发完后提交到新分支；
   3. 切换回`dev`并将内容合并，最后推送到远端的`dev`。

所以，团队合作的分支看起来就像这样：

![policy-branch-2](../.gitbook/assets/git-branch.assets/policy-branch-2.png)

## Bug分支

在软件开发过程中，`master`需要修复一个代号为`101`的bug。此时，我们会考虑创建一个分支`issue-101`来进行修复。但此时，你仍在`dev`开发新任务，并且暂时无法提交内容。Git提供了`stash`功能，帮助保护和恢复工作现场。

### 保护 & 恢复现场

#### git stash

切換到`dev`分支，使用git stash保存当前工作区的进度，并查看当前状态

```
$ git switch dev
Switched to branch 'dev'

$ git stash
Saved working directory and index state WIP on dev: 9b3427f add Merge

$ git status
On branch dev
nothing to commit, working tree clean
```

在bug修复之后，可以使用以下两种方式恢复现场：

1. 使用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除。
2. 用`git stash pop`，恢复的同时把stash内容也删了；

#### git stash apply & git stash drop

```
$ git stash apply
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   git-branch.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git stash drop stash@{0}
Dropped stash@{0} (d1d20af3356261bfb522ad6904fcc5963c20a5e9)
```

#### git stash pop

```
$ git stash pop
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   git-branch.md

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (e3bde066af3e0b7facdb0aaba987e99658bb0d95)
```

### Bug修復

```shell
$ git switch -c issue-101
Switched to a new branch 'issue-101'

$ echo "fix bug 101" >> README.md

$ git add README.md

$ git commit -m "fix bug 101"
[issue-101 687f515] fix bug 101
 1 file changed, 1 insertion(+)

$ git switch master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 7 commits.
  (use "git push" to publish your local commits)

$ git merge --no-ff -m "merge bug fix 101" issue-101
Merge made by the 'ort' strategy.
 README.md | 1 +
 1 file changed, 1 insertion(+)
```

我們在`master`分支合并了分支`issue-101`修復了bug，但dev是在`master`修復之前copy的，也就是說bug在`dev`同樣存在。

Git提供了一个`cherry-pick`命令，能夠幫助我們让我们能复制一个特定的提交到当前分支。

在`issue-101`分支提交修復的bug時，我們獲得了`commit id`——`[issue-101 687f515] fix bug 101`。

现在切換到dev分支，使用`git cherry-pick <commit-id>`即可：

```
$ git cherry-pick 687f515
[dev 2549f29] fix bug 101
 Date: Sat Aug 13 21:53:17 2022 +0800
 1 file changed, 1 insertion(+)
```

## Feature分支





## 多人协作





## Rebase





## Reference

1. [分支管理 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/896954848507552)