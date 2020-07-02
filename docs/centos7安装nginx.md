# centos7 安装 nginx

## 安装 nginx

如果还没有安装 EPEL， 可以通过以下命令安装

```sh
sudo yum install epel-release
```

> 可以添加 `-y` 参数来避免安装过程中手动确认。

输入以下命令安装 nginx

```sh
sudo yum install nginx
```

设置 nginx 开启自启动

```sh
sudo systemctl enable nginx
```

启动 nginx

```sh
sudo systemctl start nginx
```

检查 nginx 运行状态

```sh
sudo systemctl status nginx
```

（可选）开启防火墙的 80(http) 和 443(https) 端口

```sh
sudo firewall-cmd --permanent --zone=public --add-port=80/tcp
sudo firewall-cmd --permanent --zone=public --add-port=443/tcp
sudo firewall-cmd --reload
```

> 注意： 国内的云服务器厂商，`安全组` 也可能会默认屏蔽这两个端口，需要到控制台去设置。

验证是否成功启动

在浏览器输入 `http://YOUR_IP` ,将会看到 Nginx 的默认欢迎页

## 管理 nginx

启动/停止/重启 nginx

```sh
sudo systemctl start nginx # 启动
sudo systemctl stop nginx # 停止
sudo systemctl restart nginx # 重启
```

修改 nginx 配置后，重新加载

```sh
sudo systemctl reload nginx
```

设置/取消开机启动

```sh
sudo systemctl enable nginx # 开启启动
sudo systemctl disable nginx # 取消开机启动
```

## nginx 配置文件和最佳实践

1. 配置文件目录

   - 通过以上方式安装 nginx 后，相关的配置文件位置在 `/etc/nginx/` 目录下
   - nginx 主配置文件是 `/etc/nginx/nginx.conf`

2. 新增配置的最佳方式

   - 为了使 nginx 配置更易于维护，建议为每个新增的服务（域名）创建一个单独的配置文件
   - 每个独立的配置文件以 `.conf` 结尾，存放在 `/etc/nginx/conf.d` 目录下
   - 独立的配置文件建议遵循以下命名规则： 例如域名为 `abc.com`，那么配置文件命名为 `/etc/nginx/conf.d/abc.com.conf`，当然如果一个服务器部署了多个服务，也可以添加上端口号，如： `abc.3000.com.conf`

3. 重复代码块管理

   - 对于配置中的重复的代码，建议创建一个 `/etc/nginx/snippets` 文件夹存放被复用的代码块，然后在各个需要用到的 Nginx 的配置文件中引用

4. 日志文件位置

   - nginx 的日志文件(`access.log` 和 `error.log`) 位于 `/var/log/nginx/` 目录中
   - 建议为每个独立服务配置不同的日志文件位置，方便查找错误

5. 代码部署位置

   - 推荐放在以下位置之一：
   - `/home/<user_name>/<site_name>`
   - `/usr/share/nginx/html`

## 参考文章

- [2019 年如何在 CentOS 7 上安装最新版 Nginx](https://segmentfault.com/a/1190000018109309)
