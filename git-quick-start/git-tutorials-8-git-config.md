# Git 配置

## 忽略文件列表

Git 提供了 `.gitignore` 用於記錄Git工作空間下不需要提交的文件。

- 各種配置文件`.gitignore`模板：https://github.com/github/gitignore；
- 文檔手冊：[Git - gitignore Documentation (git-scm.com)](https://git-scm.com/docs/gitignore)

忽略文件的原则是：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

### Usage

1. 將需要忽略的文件或文件夾寫入即可，獨占一行；
2. `*`表示所有的；
3. `#`爲注釋；
4. 其他規則可以參考文檔手冊。

### 强制添加忽略文件

偶爾，我們會發現有些文件需要添加到Git，但添加不了，因爲他被`.gitignore`文件忽略了。這個時候，可以通過`-f`强制添加。

```shell
$ git add App.class
The following paths are ignored by one of your .gitignore files:
App.class
Use -f if you really want to add them.

$ git add -f App.class
```

### 查找忽略文件的規則

事實上，此時我們應該意識到`.gitignore`文件的規則是有問題的。使用`git check-ignore`檢查：

```
$ git check-ignore -v App.class
.gitignore:3:*.class	App.class
```

### 强制添加忽略規則中的某些文件

將`!<file>`添加到`.gitignore`文件中即可：

```shell
# 排除所有.开头的隐藏文件:
.*
# 排除所有.class文件:
*.class

# 不排除.gitignore和App.class:
!.gitignore
!App.class
```

## 配置別名