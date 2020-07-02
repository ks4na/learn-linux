# centos 7 防火墙端口配置

## 管理防火墙

```sh
sudo systemctl status firewalld # 查看防火墙状态
sudo systemctl start firewalld # 开启防火墙
sudo systemctl stop firewalld # 关闭防火墙

sudo systemctl enable firewalld # 开机启动
sudo systemctl disable firewalld # 禁止开机启动
```

## 查看已开放的端口

```sh
sudo firewall-cmd  --zone=public --list-ports
```

## 开放/关闭端口

> 以 `TCP 22 端口` 为例：

```sh
# 开启 22/tcp 端口
sudo firewall-cmd --permanent --zone=public --add-port=22/tcp

# 关闭 22/tcp 端口
sudo firewall-cmd --permanent --zone=public --remove-port=22/tcp
```

## 重载防火墙配置

防火墙配置更新之后，需要进行重新加载来让新配置生效

```sh
sudo firewall-cmd --reload
```
