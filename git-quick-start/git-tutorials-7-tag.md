# Git 标签

Git标签实际是一个指向commit的指针，不能够移动，相当于commit的别名（最好是有意义且好记的名字）。

## 创建标签

标签是与commit绑定的而不是分支。使用`git tag <name> [<commit-id>]`为commit添加标签，commit-id 默认为最新的commit，即HEAD。

可以使用`-a <name> `指定标签名，然后使用`-m "explain text"`添加说明文字：`git tag -a v0.1 -m "version 0.1 released" 4075e3b `

### 示例

```shell
# 切换到对应的分支
$ git branch
* dev
  master
$ git switch master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.

# 为最新的commit添加tag
$ git tag v1.0

# 查看commit历史
$ git log --pretty=oneline --abbrev-commit
2f0bc0d (HEAD -> master, tag: v1.0, origin/master) add dot
eeef06a remove test.txt
0024cbe merge bug fix 101
687f515 fix bug 101
b72bb0f merge without no-ff
2bc5f94 add merge

# 为需要的commit打标签
$ git tag v0.9 2bc5f94

#查看当前tag（按字母排序）
$ git tag
v0.9
v1.0

# 查看tag详细信息
$ git show v0.9
commit 2bc5f94af8ef791945557b0dc0f6c60e7549b751 (tag: v0.9)
Author: merlotliu <meetlouisliu@163.com>
Date:   Sat Aug 13 21:42:13 2022 +0800

    add merge

diff --git a/README.md b/README.md
index ea69c1a..fac7020 100644
--- a/README.md
+++ b/README.md
@@ -3,3 +3,4 @@ Git is free software distributed under the GPL.
 Git has a mutable index called stage.
 Git tracks changes.
 Creating a new branch is quick and simple.
+Merge branch without fast forward
...

# 添加标签时添加说明文字
$ git tag -a v0.1 -m "version 0.1 released" 4075e3b
$ git show v0.1
tag v0.1
Tagger: merlotliu <meetlouisliu@163.com>
Date:   Thu Aug 18 17:21:44 2022 +0800

version 0.1 released

commit 4075e3b059c515f7e65911948d1e664b2f4815b0 (tag: v0.1)
Author: merlotliu <meetlouisliu@163.com>
Date:   Sat Aug 13 20:45:53 2022 +0800

    understand how stage works

diff --git a/LICENSE b/LICENSE
new file mode 100644
index 0000000..e69de29
```

## 操作标签

### 推送标签

推送单个标签：`git push origin <tag-name>`

```
$ git push origin v1.0
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:merlotliu/git-prac-repo.git

 * [new tag]         v1.0 -> v1.0
```

將所有標簽推送到遠程：`git push origin --tags`

```
$ git push origin --tags
Enumerating objects: 1, done.
Counting objects: 100% (1/1), done.
Writing objects: 100% (1/1), 165 bytes | 165.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
To github.com:merlotliu/git-prac-repo.git
 * [new tag]         v0.1 -> v0.1
 * [new tag]         v0.9 -> v0.9
```

### 删除标签

#### 本地

使用`git tag -d <tag-name>`刪除本地標簽：

```
$ git tag -d v0.1
Deleted tag 'v0.1' (was be1badf)
```

如果沒有推送到遠程，這樣就已經把標簽刪除了。

#### 远程

刪除遠程標簽，分兩步：

1. 首先要在本地將標簽刪除先，操作跟上面一樣。

2. 刪除遠程標簽：

   ```
   $ git push origin :refs/tags/v0.1
   To github.com:merlotliu/git-prac-repo.git
    - [deleted]         v0.1
   ```

   刪除完成後，可以登錄 Github 查看操作是否成功。

## Reference

1. [标签管理 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/900788941487552)