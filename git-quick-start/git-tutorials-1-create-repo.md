# 创建版本库

## 1 创建目录

切换到一个合适的位置，创建一个空目录：

```shell
$ mkdir git-notes
$ cd git-notes
$ pwd
/c/Users/Danie/git-notes
```

**Notes**：尽量保证路径不出现中文。

## 2 初始化仓库

在创建好的目录下，使用`git init`命令将目录变成Git可管理的仓库：

```shell
$ git init
Initialized empty Git repository in C:/Users/Danie/git-notes/.git/
```

初始化完成后，会多出一个`.git`的目录，这一目录的作用是跟踪版本库的变更。可以使用`ls -ah`查看路径下所有文件（包括隐藏目录）。

同时，可以看到路径后多了`(Master)`，标识当前分支名，通常`Master`为默认分支。

## 3 添加文件到版本库

创建并编写`README.md`文件，步驟如下：

```shell
$ touch README.md
$ echo "Git is a version contorl system." > README.md
$ echo "Git is free software." >> README.md
$ cat README.md
Git is a version control system.
Git is free software.
```

文件一定要存在剛才創建好的`git-notes`目錄或子目錄下。

### git add

使用`git add <filename>`告訴Git，將文件提交到倉庫。

```shell
$ git add README.md
```

如果沒有問題是不會有任何顯示的，畢竟Unix的哲學——沒有消息就是好消息。

### git commit

用命令`git commit -m "message"`告诉Git，把文件提交到仓库：

```shell
$ git commit -m "wrote a readme file"
[master (root-commit) 8fa9ef5] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 README.md
```

`-m`為選項，後面跟上本次修改的説明，爲了後期版本的維護，我們應該保證這一信息的準確和有意義。

`git commit`命令执行成功后会告诉你，`1 file changed`：1个文件被改动（我们新添加的readme.txt文件）；`2 insertions`：插入了两行内容（readme.txt有两行内容）。

### add 和 commit分開的意義

我們可以多次通過`add`添加文件到暫存區，并使用一次`commit`將暫存區的文件全部提交到版本庫的當前分支。

## 幫助

在不熟悉的命令後面跟上`-h`，可查看其用法：

```shell
$ git commit -h
usage: git commit [<options>] [--] <pathspec>...

    -q, --quiet           suppress summary after successful commit
    -v, --verbose         show diff in commit message template

Commit message options
    -F, --file <file>     read message from file
    --author <author>     override author for commit
    --date <date>         override date for commit
    -m, --message <message>
                          commit message
    -c, --reedit-message <commit>
                          reuse and edit message from specified commit
    -C, --reuse-message <commit>
                          reuse message from specified commit
    --fixup [(amend|reword):]commit
                          use autosquash formatted message to fixup or amend/reword specified commit
    --squash <commit>     use autosquash formatted message to squash specified commit
    --reset-author        the commit is authored by me now (used with -C/-c/--amend)
    --trailer <trailer>   add custom trailer(s)
    -s, --signoff         add a Signed-off-by trailer
    -t, --template <file>
                          use specified template file
    -e, --edit            force edit of commit
    --cleanup <mode>      how to strip spaces and #comments from message
    --status              include status in commit message template
    -S, --gpg-sign[=<key-id>]
                          GPG sign commit

Commit contents options
    -a, --all             commit all changed files
    -i, --include         add specified files to index for commit
    --interactive         interactively add files
    -p, --patch           interactively add changes
    -o, --only            commit only specified files
    -n, --no-verify       bypass pre-commit and commit-msg hooks
    --dry-run             show what would be committed
    --short               show status concisely
    --branch              show branch information
    --ahead-behind        compute full ahead/behind values
    --porcelain           machine-readable output
    --long                show status in long format (default)
    -z, --null            terminate entries with NUL
    --amend               amend previous commit
    --no-post-rewrite     bypass post-rewrite hook
    -u, --untracked-files[=<mode>]
                          show untracked files, optional modes: all, normal, no. (Default: all)
    --pathspec-from-file <file>
                          read pathspec from file
    --pathspec-file-nul   with --pathspec-from-file, pathspec elements are separated with NUL character
```

## Reference

1. [创建版本库 - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/896827951938304)