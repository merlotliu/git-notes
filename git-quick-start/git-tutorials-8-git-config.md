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

使用`git config --global alias.<alias-name> <git-command>`命令可以为相关命令配置别名：

```shell
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch

# log command simplify
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

`--global`参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用。不添加該選項則設置只對當前倉庫有效。

### 配置文件

每個倉庫的配置文件存放在`.git/config`文件中：

```shell
$ cat .git/config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        ignorecase = true
[remote "origin"]
        url = git@github.com:merlotliu/git-prac-repo.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master
[branch "dev"]
        remote = origin
        merge = refs/heads/dev
```

相應的配置也可以通過修改配置文件。

## 搭建Git服務器

### 安裝Git

#### 第一步，安装`git`：

```
$ sudo apt-get install git
```

#### 第二步，创建一个`git`用户，用来运行`git`服务：

```
$ sudo adduser git
```

#### 第三步，创建证书登录：

不然每次都需要输入密码，非常麻烦。

收集所有需要登录的用户的公钥，就是他们自己的`id_rsa.pub`文件，把所有公钥导入到`/home/git/.ssh/authorized_keys`文件里，一行一个。



客户端进行以下操作：

如果没有的话，使用`ssh-keygen -t rsa -C "<email>"` 生成，可以修改生成的文件名加以区分不同的密钥。

然后，先使用 `ssh-agent bash` ，再使用 `ssh-add ~/.ssh/<id-rsa-name>` 添加私钥。

服务端进行以下操作：

首先查看，`/etc/ssh/sshd_config` 文件，找到：

```shell
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile %h/.ssh/authorized_keys
```

如果被注释则取消注释，接着重启 sshd 服务 `service sshd restart`。

然后，进入到 `git` 用户的家目录：

- 修改 `.ssh` 权限 ： `chmod 700 /home/git/.ssh`；（文件夹需要可执行权限才能打开）
- 修改 `authorized_keys` 权限 ： `chmod 600 /home/git/.ssh/authorized_keys`；

然后将刚才在客户端生成添加的私钥所对应的公钥 `<id-rsa-name>.pub`的内容复制到 ` /home/git/.ssh/authorized_keys`文件中，一行一个。

测试密钥是否添加成功：

```
ssh -T git@<ip-address>
# 如果是 github 后面就是 github.com
# 本地就填写 ip 地址
# 根据输出信息判断是否成功
```



#### 第四步，初始化Git仓库：

先选定一个目录（随意选取，/home/git/也是不错的选择）作为Git仓库，假定是`/srv/<reposity-name>.git`，在`/srv`目录下输入命令：

```
$ sudo git init --bare <reposity-name>.git
```

Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以`.git`结尾。然后，把owner改为`git`：

```
$ sudo chown -R git:git <reposity-name>.git
# 当然也可以放在 /home/git/目录下
```

#### 第五步，禁用shell登录：

出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑`/etc/passwd`文件完成。找到类似下面的一行：

```
git:x:1001:1001:,,,:/home/git:/bin/bash
```

改为：

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

这样，`git`用户可以正常通过ssh使用git，但无法登录shell，因为我们为`git`用户指定的`git-shell`每次一登录就自动退出。

#### 第六步，克隆远程仓库：

现在，可以通过`git clone`命令克隆远程仓库了，在各自的电脑上运行：

```
$ git clone git@server:/srv/<reposity-name>.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
```

剩下的推送就简单了。

### 管理公钥

将每个人的公钥收集起来放到服务器的`/home/git/.ssh/authorized_keys`文件里。（小团队）

### 管理权限

可使用[Gitosis](https://github.com/res0nat0r/gitosis)（略）

## Reference

1. [自定义Git - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/896043488029600/900785521032192)