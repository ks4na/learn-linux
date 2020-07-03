# centos 7 用户管理

> 为了系统安全考虑，日常使用 linux 时最好不要使用 root 用户，可以新建一个用户。

## 基础命令

```sh
useradd newuser # 创建新用户 newuser，如果没有主目录，需要添加 `-m` 参数

passwd newuser # 为 newuser 设置密码

userdel newuser # 删除用户 newuser， 如果需要同时删除主目录，需要加上 `-r` 参数
```

> 新创建的用户会在 `/home` 目录下拥有一个对应用户名的主目录，如果没有主目录，需要在创建时添加上 `-m` 参数。

## 添加 sudo 权限

### 方法一

普通用户没有 sudo 权限，执行 sudo 相关命令会失败，可以使用如下命令将用户添加到超级用户组：

```sh
usermod -aG sudo newuser
```

> `-G` 的含义是把用户添加到指定的用户组，但是会自动从其他组中删除。  
> `-a` 的含义是追加，但不会从其他组中删除。

> centos7 中没有 `sudo` 组，超级用户组为 `wheel` 。

### 方法二

输入 `visudo` 并编辑，在

```sh
root    ALL=(ALL)       ALL
```

这一行下面添加类似语句，`root` 替换成用户名，如：

```sh
newuser    ALL=(ALL)       ALL
```

## 切换用户

```sh
su root # 切换到 root 用户（su -> switch user）
```

## 查看用户信息

```sh
id newuser
```

输出类似如下的用户信息：

```sh
uid=1001(newuser) gid=1001(newuser) groups=1001(newuser)
```
