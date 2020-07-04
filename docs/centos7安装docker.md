# centos 7 安装 docker

> Docker 运行在 CentOS 7 上，要求系统为 64 位、系统内核版本为 3.10 以上；  
> Docker 只能部署在 KVM 或者 XEN 架构的 VPS 中。

## 查看你当前的内核版本

uname -r

## 安装 Docker

sudo yum -y install docker

## 启动 Docker 后台服务

sudo systemctl start docker

## 设置开机自启动

sudo systemctl enable docker
