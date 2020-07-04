# docker 安装 mysql

## 拉取 mysql 镜像

访问 [dockerhub](https://hub.docker.com/_/mysql?tab=tags) ,选择想要拉取的 `tag` 版本，复制对应的 docker 命令

```sh
docker pull mysql:5.7.30
```

## 启动 mysql

根据 docker 官网介绍的命令：

```sh
sudo docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
```

其中 `some-mysql` 为容器名， `my-secret-pw` 为数据库密码， `tag` 为 mysql 镜像的 tag。

> `MYSQL_ROOT_PASSWORD`为环境变量，更多的环境变量参阅 [docker 官网](https://hub.docker.com/_/mysql?tab=description)的 `Environment Variables` 一节。

启动 `mysql` 容器时需要进行修改，添加端口号映射 `-p <宿主机端口号>:<容器端口号>`，以及重启策略，
在家目录下新建 `docker-cmds` 存放 `docker` 容器的相关启动命令：

```sh
cd ~ && mkdir docker-cmds # 切换到家目录，创建 `docker-cmds` 目录

vi docker-cmds/mysql-startup.sh
```

将修改后的命令添加到 `mysql-startup.sh` 文件中：

```sh
# sudo docker run --name <container-name> -e MYSQL_ROOT_PASSWD=<mysql-passwd> -p <host-expose-port>:<container-expose-port> --restart=unless-stopped -d mysql:<tag>
sudo docker run --name mysql -e MYSQL_ROOT_PASSWD=root -p 3306:3306 --restart=unless-stopped -d mysql:5.7.30
```

保存后修改文件权限：

```sh
chmod +x mysql-startup.sh
```

然后执行该文件：

```sh
./mysql-startup.sh
```

## 验证 mysql 是否启动成功

首先查看 docker 容器是否正在运行：

```sh
sudo docker ps
```

如果没有该容器信息，而 `docker ps -a` 可以查看到该容器信息，可以查看日志定位错误原因：

```sh
docker logs mysql # `mysql` 为容器名
```

然后可以通过 navicat 远程连接 mysql 查看是否可访问，需要注意指定的主机端口是否开放。
