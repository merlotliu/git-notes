---
title: Git 子模块
date: 2023-01-07 13:41:25
updated: 2023-01-07 13:41:25
tags: 
- [git]
categories: 
- [git]
comments: true
---

# Git 子模块

以下是我在项目过程中使用的相关操作。

## 克隆仓库

### 情况1: 直接克隆包含子模块全部的内容

如果需要主存储库和子模块的全部内容代码，推荐使用：

```
git clone --recursive <URL to Git repo>
```

### 情况2: 主存储库先克隆或已存在

如果是一个很大的合作项目，个人仅维护一部分且仅使用到一部分的子模块，推荐以下的克隆方式。

首先克隆主存储库的全部内容：

```shell
git clone <URL to Git repo>
```

在这一情况下，子模块只会在对应路径出现一个库文件夹。

如果不是自己的项目，可以通过 `cat .gitmodules` 或 `git submodule status ` 查看有哪些子模块。

然后，根据当前项目需要，选择需要克隆的子模块初始化：

```shell
git submodule init <path1 path2 ...>
```

如果不添加路径，则会在本地仓库注册所有的子模块（将 `.gitmodules` 中子模块信息添加到 `.git/config`）。

因此，我们会发现这一语句被翻译为**注册**可能更为贴切，至少对我而言更能理解其实际功能。

接着，获取当前主存储库指向的子模块提交：

```shell
git submodule update <path1 path2 ...>
```

如果不添加路径，则会下载所有已经 init 的子模块（注册在  `.git/config`）。

值得注意的是，获取子模块内容之前必须注册。

事实上，以上注册和获取子模块提交的操作，可以使用以下语句代替：

```shell
git submodule update --init <path1 path2 ...>
```

到目前为止，主存储库和需要的子模块克隆完毕。

## 添加子模块

向存储库添加一个子模块：

```shell
git submodule add <URL to Git repo>
```

目前我有一个存储库 Parent，我想在里面添加一个子存储库 Child，于是在 Parent 目录下，使用以下命令：

```shell
cd Parent
git submodule add <URL to Child repo>
```

注意：git 子模块的功能是依赖服务器的，即 url 必须是有效的网络路径，如 `https://github.com/username/repos`。如果是本地路径，在云端同步的代码仓库是找不到对应文件的。这意味着，`git submodule` 只记录子存储库的 `git commit` ，而不包含实际文件。 

添加成功后，会在主存储库目录下的 `.gitmodules` 中，添加以下信息：

```shell
[submodule "Child"]
	path = Child
	url = https://github.com/username/repos
```

并在 `.git/modules` 目录下添加子存储库的 git 信息，修改 `.git/config`:

```
[submodule "Child"]
	url = https://github.com/username/repos
	active = true
```

## 更新子模块

### 更新子模块提交

到目前为止，我们知道 `git submodule update --init` 只会获取主存储库当前的子模块提交，而不会自动更新子模块。

如果子模块内容需要获取最新提交，使用以下命令：

```shell
git submodule update --remote <path1 path2 ...>
```

这一操作，相当于在对应的子模块下 `git pull` 。

### 更新子模块的 Remote

如果子模块的远程库路径发生变动，或本地库绑定错误，可以使用以下的方式修改。主要内容分两步：

- 第一步，修改 `.gitmodules` 中对应子模块的 url；
- 第二步，同步更新到子模块：将 `.gitmodules`作为标准，将内容同步到 `.git/config` 和 对应子模块的 Remote；

#### 方法1

按照主要内容操作，先手动修改`.gitmodules` 中对应子模块的 url。然后使用以下命令同步更新：

```shell
git submodule sync [--recursive] [--] [<path>...]
```

如果不添加路径，表示更新所有子模块。

#### 方法2

合并两步操作：修改 `.gitmodules`、`.git/config` ，并同步更新到 `<path>`路径的 repo 的 remote：

```shell
git submodule set-url [--] <path> <newurl>
```

#### 什么时候使用 sync ？什么时候使用 set-url ?

- 事实上，当项目在本地改动，当然是最省事的方式 `set-url`；
- 然而如果远程库被修改，则需要 `git pull` 获取主存储库的最新信息，其中就包含了 `.gitmodules` ，如果其发生了改变，则需要使用 `git submodule sync` 更新子存储库的 Remote，所以在**克隆或者更新存储库后**，应该 `sync`；
- 其次，在现有本地存储库，之后再创建远程库的情况下，建议使用 `set-url` 修改 Remote。

## 删除子模块

删除子模块分三步：

- 第一步，从主存储库取消注册（修改`.git/config`文件），并清除对应目录下的文件。使用以下命令即可：

  ```shell
  git submodule deinit [path]
  ```

- 第二步，从主存储库删除子模块（修改`.gitmodules`文件），并删除子模块对应目录。使用以下命令：

  ```
  git rm -f [path]
  ```

- 第三步，删除`.git/modules` 下对应子模块的 git 记录。
  这一步，对于删除子模块其实可以**省略**。不过，对于新手小白而言，在后面如果添加同名的 repo，可能会出现一些不能理解的错误。

事实上，可以直接修改 `.git/config` `.gitmodules` 以及 `.git/modules`，命令仅简化了相关操作。

## Reference 

1. [使用子模块和子树来管理 Git 项目 | Linux 中国](https://zhuanlan.zhihu.com/p/143100657)
1. [Managing Git projects with submodules and subtrees](https://opensource.com/article/20/5/git-submodules-subtrees)
1. [git如何删除子模块](https://blog.csdn.net/ownfire/article/details/45477811)
