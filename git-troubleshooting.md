# Git Toubleshooting

## warning: in the working copy of 'README.md', LF will be replaced by CRLF the next time Git touches it

### 原因

windows中的换行符为 CRLF， 而在Linux下的换行符为LF。

在Git中，`core.autocrlf`的默認爲`true`。意味著，將工作區的文件提交到暫存區時，Git會檢查文件中的換行符，并將LF轉化爲CRLF。

### 解決

設置`core.autocrlf`為`false`，並刪除暫存區文件，重新提交。

```
# 禁用自動轉化
$ git config --global core.autocrlf false 
# 清除暫存區文件 "git rm --cached <file>..."
$ git rm --cached <file>
# 然後重新添加文件
```

### Reference

1. [错误 warning: LF will be replaced by CRLF in README.md. - ryxiong728 - 博客园 (cnblogs.com)](https://www.cnblogs.com/ryxiong-blog/p/11268952.html)