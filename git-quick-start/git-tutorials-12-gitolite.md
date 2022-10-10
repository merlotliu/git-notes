# Gitolite

## 简要说明

```shell
# 创建 git 用户
sudo adduser git
# 如果需要 sudo 权限,则 
sudo adduser git sudo 

# 设置密码，没有密码是登陆不了的
sudo passwd git

# git@gitserver
- gitserver : 本地 git 服务器 ip 或主机名；
- git : git 用户账号
```

## 准备工作

1. 在管理员所在的终端生成密钥，并将`.pub`，命名为 `<your-name>.pub`（这个名字将应用 gitolite 配置中）；
2. 登录 git 用户；（没有设置密码先通过 `sudo passwd git` 设置）
3. 将 git 用户的 `$HOME/.ssh/authorized_keys` 也就是 `/home/git/.ssh/authorized_keys` 备份或删除；

## 安装

```shell
git clone https://github.com/sitaramc/gitolite

# 创建安装目录
mkdir -p $HOME/bin
# 或
mkdir ~/bin -p

# 安装
gitolite/install -to $HOME/bin
```

## 设置管理员

```shell
# 添加了系统路径的情况下
gitolite setup -pk <your-name>.pub

# 如果找不到
# 直接使用 ~/bin/gitolite 设置
# 以下三条都是一样的
/home/git/bin/gitolite setup -pk <your-name>.pub
$HOME/bin/gitolite setup -pk <your-name>.pub
~/bin/gitolite setup -pk <your-name>.pub
```

## 测试连接

设置好之后，在管理员端连接 gitserver ，连接成功则进行下一步。

失败的原因通常是公钥没有添加成功，重新尝试以上操作。

```shell
ssh git@gitserver
```

## 添加用户和仓库

通过修改 `gitolite-admin` 完成。

将 `gitolite-admin` clone 到管理员端，`cd gitolite-admin/ && ls`：

```bash
~$ cd gitolite-admin/ && ls
conf  keydir

# 其中 conf/gitolite.conf 为配置文件
# keydir 存放用户的公钥，通常命名为 <name>.pub
# 其中的 <name> 对应 gitolite.conf 中权限管理使用的 name
```

### conf/gitolite.conf

```shell
repo foo
    RW+         =   alice
    RW          =   bob
    R           =   carol
# 这份样例中
# alice 对仓库 foo 有完全控制权限
# bob 有读写权限
# carol 有读权限
```

### keydir

```shell
~/gitolite-admin$ ls keydir/
merlotliu.pub  zx.pub
```

## 访问权限样例

Gitolite's access rules are very powerful. The simplest use was already shown above. Here is a slightly more detailed example:

```
repo foo
    RW+                     =   alice
    -   master              =   bob
    -   refs/tags/v[0-9]    =   bob
    RW                      =   bob
    RW  refs/tags/v[0-9]    =   carol
    R                       =   dave
```



Here's what these example rules say:

- alice can do anything to any branch or tag -- create, push, delete, rewind/overwrite etc.
- bob can create or fast-forward push any branch whose name does not start with "master" and create any tag whose name does not start with "v"+digit.
- carol can create tags whose names start with "v"+digit.
- dave can clone/fetch.

Please see the main documentation linked above for all the gory details, as well as more features and examples.

## 组

Gitolite allows you to group users or repos for convenience. Here's an example that creates two groups of users:

```
@staff      =   alice bob carol
@interns    =   ashok

repo secret
    RW      =   @staff

repo foss
    RW+     =   @staff
    RW      =   @interns
```



Group lists accumulate. The following two lines have the same effect as the earlier definition of @staff above:

```
@staff      =   alice bob
@staff      =   carol
```

You can also use group names in other group names:

```
@all-devs   =   @staff @interns
```

Finally, @all is a special group name that is often convenient to use if you really mean "all repos" or "all users".

## 命令

Users can run certain commands remotely, using ssh. Running

```
ssh git@host help
```

prints a list of available commands.

The most commonly used command is "info". All commands respond to a single argument of "-h" with suitable information.

If you have shell on the server, you have a lot more commands available to you; try running "gitolite help".

## 修改 .ssh 权限

```shell
chmod 700 /home/git/.ssh
# 文件夹需要可执行权限才能打开
chmod 600 /home/git/.ssh/authorized_keys
```

## 禁止 git 用户登录

为了安全的考虑，通常在配置完成后，我们需要将 git 用户的登录功能禁止。

很多帖子中使用修改 `/ect/passwd` 的方式，将 git 用户最后一部分修改为 `/usr/git/bin/git-shell`，这一方式将会引发一些错误，相关 `gitolite` 命令无法执行。

所以，我们可以使用删除 git 用户密码的方式来限制登录：

```shell
$sudo passwd -d git
```

## Reference

1. [Gitosis — Git中文手册 0.1 文档 (pythontab.com)](https://docs.pythontab.com/github/gitbook/Git-on-the-Server/Gitosis.html)
1. [sitaramc/gitolite: Hosting git repositories -- Gitolite allows you to setup git hosting on a central server, with very fine-grained access control and many (many!) more powerful features. (github.com)](https://github.com/sitaramc/gitolite)