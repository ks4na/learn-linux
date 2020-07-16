# centos 7 配置 https 证书（docker 方式）

## 前言

使用 certbot 生成 LetsEncrypt 的免费的 SSL 泛域名证书并自动续期。

> 本文使用 docker 方式来申请和续期。

> 为了过程的方便，需要使用 DNS 插件，所以事先将域名解析转移到提供了插件的 DNS 服务提供商（如： cloudflare），具体过程略。

### 拉取镜像

```sh
docker pull certbot/dns-cloudflare
```

### 新建配置文件

根据 [certbot-dns-cloudflare](https://certbot-dns-cloudflare.readthedocs.io/en/stable/) 插件的配置要求新建配置文件：

> 以 `example.com` 为例。

```sh
cd ~ && mkdir -p .secrets/certbot/cloudflare # 为了方便管理，在家目录下创建专门的文件夹存放配置信息

vi .secrets/certbot/cloudflare/example.com.ini # 创建 <domian>.ini 文件
```

然后填写如下配置信息

```sh
# Cloudflare API token used by Certbot
dns_cloudflare_api_token = <api_token> # api_token 的获取方式参见插件的文档
```

更改该文件访问权限（可选，不更改的话 certbot 会报红色警告）

```sh
chmod 600 example.com.ini
```

### 申请证书

编写申请证书的 shell 脚本

```sh
cd ~ && mkdir -p docker-cmd/cerbot/example.com # 为了方便管理，在家目录下创建专门目录存放脚本文件

vi cert.sh # 创建 cert.sh 用于首次申请证书
```

填写如下命令

```sh
#!/bin/bash
sudo docker run -it --rm --name certbot \
  -v "/etc/letsencrypt:/etc/letsencrypt" \
  -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
  -v "/home/yuusha/.secrets:/.secrets" \ # 需要改动的地方，指定家目录下的 `.secrets` 映射到容器中 `/.secrets` 目录
  certbot/dns-cloudflare certonly \
  --dns-cloudflare-credentials /.secrets/certbot/cloudflare/example.com.ini \ # 需要改动的地方，指定 `<domain>.ini` 的路径
  --dns-cloudflare-propagation-seconds 120 \ # 需要改动的地方，指定验证 dns 记录之前等待的秒数
  --server https://acme-v02.api.letsencrypt.org/directory \
  -d *.example.com # 需要改动的地方，指定申请证书的域名 `*.example.com`
```

更改文件权限

```sh
chmod +x cert.sh
```

然后执行该文件

```sh
./cert.sh
```

回答几个简单询问，需要注意的是验证方式选 `DNS TXT`，然后就申请成功了。生成的相关证书在 `/etc/letsencrypt/live` 目录下，按域名分为不同的目录。

### 自动续期

> Let’s Encrypt 的证书有效期为 90 天。为了实现自动续期，可以使用定时任务。

编写自动续期的 shell 脚本，在 `cert.sh` 同目录下创建 `renew.sh`

```sh
vi renew.sh # 创建 renew.sh 用于首次申请证书
```

```sh
#!/bin/bash
sudo docker run -i --rm --name certbot \
  -v "/etc/letsencrypt:/etc/letsencrypt" \
  -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
  -v "/home/yuusha/.secrets:/.secrets" \ # 需要改动的地方，指定家目录下的 `.secrets` 映射到容器中 `/.secrets` 目录
  certbot/dns-cloudflare -q renew
```

> 注意:  
> `renew.sh` 脚本中 docker 命令不加 `-t` 参数，使用 `crontab` 定时任务运行 docker 命令时如果有 `-t` 参数会报错 `the input device is not a TTY`.

更改文件权限

```sh
chmod +x renew.sh
```

然后设置定时任务

```sh
sudo crontab -e # 创建定时任务
```

填写如下代码，实现每个月 1 号、15 号的 3 -4 点执行一次 `renew.sh`

```sh
0 3 1,15 * * perl -e 'sleep int(rand(3600))' && /home/yuusha/docker-cmd/cerbot/example.com/renew.sh > /dev/null  && sudo systemctl reload nginx
```

> 注：使用 docker 的方式续期无法使用 `--renew-hook` 钩子函数，所以直接添加 `&& sudo systemctl reload nginx`。

## 参考文章

- [certbot](https://certbot.eff.org/lets-encrypt/centosrhel7-nginx)
- [running with docker](https://certbot.eff.org/docs/install.html#running-with-docker)
- [certbot-dns-cloudflare](https://certbot-dns-cloudflare.readthedocs.io/en/stable/)
- [使用 Docker 生成 LetsEncrypt 证书](https://xiaozhou.net/generate-letsencrypt-cert-via-docker-2019-03-13.html)
