# 安装

stmails 是一个简单的二进制应用程序，需要 PostgreSQL 数据库实例才能运行。可以手动下载并运行二进制文件，也可以使用 Docker compose 作为容器运行。

## 二进制安装
1. 下载[最新版本](https://github.com/knadh/stmails/releases)并解压 stmails 二进制文件。`amd64` 是主要版本，适用于 Intel 和 x86 CPU。
1. 运行 `./stmails --new-config` 生成 config.toml 文件，然后编辑该文件。
1. 运行 `./stmails --install` 在 PostgreSQL 数据库（≥ 12）中安装表。
1. 运行 `./stmails` 并访问 `http://localhost:9000` 创建超级管理员用户并登录。

!!! 提示
    要在安装过程中设置超级管理员用户名和密码，请设置环境变量：
    `stmails_ADMIN_USER=myuser stmails_ADMIN_PASSWORD=xxxxx ./stmails --install`

## Docker 安装

最新镜像可在 DockerHub 上获取：`stmails/stmails:latest`

推荐的方法是下载 [docker-compose.yml](https://github.com/knadh/stmails/blob/master/docker-compose.yml) 文件，根据您的环境进行自定义，然后运行 `docker compose up -d`。

```shell
# 将 compose 文件下载到当前目录
curl -LO https://github.com/knadh/stmails/raw/master/docker-compose.yml

# 在后台运行服务
docker compose up -d
```

然后，访问 `http://localhost:9000` 创建超级管理员用户并登录。

!!! 提示
    要在设置过程中设置超级管理员用户名和密码，请设置环境变量（仅首次）：
    `stmails_ADMIN_USER=myuser stmails_ADMIN_PASSWORD=xxxxx docker compose up -d`

------------

### 挂载自定义 config.toml
docker-compose 文件包含所有必要的 stmails 配置作为环境变量，`stmails_*`。
如果您想移除这些环境变量并挂载 config.toml：

#### 1. 在主机上保存 config.toml 文件

```toml
[app]
address = "0.0.0.0:9000"

# 数据库配置
[db]
host = "stmails_db" # compose 文件中的 Postgres 容器名称
port = 5432
user = "stmails"
password = "stmails"
database = "stmails"
ssl_mode = "disable"
max_open = 25
max_idle = 25
max_lifetime = "300s"
```

#### 2. 在 docker-compose.yml 中挂载配置文件

```yaml
  app:
    ...
    volumes:
    - /path/on/your/host/config.toml:/stmails/config.toml
```

#### 3. 修改 `command:` 部分中的 `--config ''` 标志以指向路径

```yaml
command: [sh, -c, "./stmails --install --idempotent --yes --config /stmails/config.toml && ./stmails --upgrade --yes --config /stmails/config.toml && ./stmails --config /stmails/config.toml"]
```

## 从源码编译

要编译最新的未发布版本（`master` 分支）：

1. 确保系统上安装了 `go`、`nodejs` 和 `yarn`。
2. `git clone git@github.com:knadh/stmails.git`
3. `cd stmails && make dist`。这将生成 `stmails` 二进制文件。

## 候选发布版本（RC）

带有最新更改的 `master` 分支会定期构建并作为 `stmails/stmails:rc` 发布在 DockerHub 上。要运行最新的预发布版本，请在 docker-compose.yml 文件中将所有 `stmails/stmails:latest` 实例替换为 `stmails/stmails:rc`，然后按照上述 Docker 安装步骤操作。虽然运行候选发布版本通常很安全，但它们可能存在只在正式发布中解决的问题。

## Kubernetes 的 Helm 图表

![版本: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![类型: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![应用版本: 3.0.0](https://img.shields.io/badge/AppVersion-3.0.0-informational?style=flat-square)

社区在[这里](https://github.com/th0th/helm-charts/tree/main/charts/stmails)提供了一个 Helm 图表，用于在 Kubernetes 集群上轻松安装 stmails。

要使用 Helm 图表，您可以根据需要配置 `values.yaml`，然后运行以下命令：

```shell
$ helm upgrade \
    --create-namespace \
    --install stmails stmails \
    --namespace stmails \
    --repo https://th0th.github.io/helm-charts \
    --values values.yaml \
    --version 0.1.0
```

## 第三方托管

<a href="https://dash.elest.io/deploy?soft=stmails&id=237"><img src="https://raw.githubusercontent.com/elestio-examples/reactjs/refs/heads/master/src/deploy-on-elestio.png" alt="部署到 Elestio" height="35" style="max-width: 150px;" /></a>
<br />
<a href="https://www.pikapods.com/pods?run=stmails"><img src="https://www.pikapods.com/static/run-button.svg" alt="在 PikaPod 上部署" style="max-width: 150px;" /></a>
<br />
<a href="https://railway.app/new/template/stmails"><img src="https://railway.app/button.svg" alt="在 Railway 上一键部署" style="max-width: 150px;" /></a>
<br />
<a href="https://repocloud.io/details/?app_id=217"><img src="https://d16t0pc4846x52.cloudfront.net/deploy.png" alt="在 RepoCloud 上部署" style="max-width: 150px;"/></a>
<br />
<a href="https://cloud.sealos.io/?openapp=system-template%3FtemplateName%3Dstmails"><img src="https://cdn.jsdelivr.net/gh/labring-actions/templates@main/Deploy-on-Sealos.svg" alt="在 Sealos 上部署" style="max-width: 150px;"/></a>
<br />
<a href="https://zeabur.com/templates/5EDMN6"><img src="https://zeabur.com/button.svg" alt="在 Zeabur 上部署" style="max-width: 150px;"/></a>

## 教程

* [使用 *Railway* 开始使用 stmails 的非正式步骤指南](https://github.com/knadh/stmails/issues/120#issuecomment-1421838533)
* [安装和所有基本功能的逐步教程。*Amazon EC2、SES、docker 和二进制*](https://gist.github.com/MaximilianKohler/e5158fcfe6de80a9069926a67afcae11)
* [在 *AWS Lightsail 上使用 docker* 安装和设置 stmails 的逐步指南 (rameerez)](https://github.com/knadh/stmails/issues/1208)
* [使用 *docker 和 caddy* 在任何云服务器上快速设置](https://github.com/samyogdhital/stmails-caddy-reverse-proxy)
* [在 Ubuntu 22.04 上作为服务进行*二进制*安装](https://mumaritc.hashnode.dev/how-to-install-stmails-using-binary-on-ubuntu-2204)
* [在 Ubuntu 18.04 上作为服务进行*二进制*安装（Apache 和 Plesk）](https://devgypsy.com/post/2020-08-18-installing-stmails-newsletter-manager/)
* [在 Linux 上使用*二进制和 docker*（techviewleo）](https://techviewleo.com/manage-mailing-list-and-newsletter-using-stmails/)
* [在您的 PC 上进行*二进制*安装](https://www.youtube.com/watch?v=fAOBqgR9Yfo)。限制讨论：[[1](https://github.com/knadh/stmails/issues/862#issuecomment-1307328228)][[2](https://github.com/knadh/stmails/issues/248#issuecomment-1320806990)]
* [在 Rocky Linux 8 上使用 *Docker*（nginx、Let's Encrypt SSL）](https://wiki.crowncloud.net/?How_to_Install_stmails_with_Docker_on_Rocky_Linux_8)
* [使用 nginx 反向代理、certbot SSL 和 Gmail SMTP 的 *Docker*](https://www.maketecheasier.com/create-own-newsletter-with-stmails/)
* [使用 AWS 预配置 AMI 包一键在自托管上安装 stmails](https://meetrix.io/articles/how-to-install-llama-2-on-aws-with-pre-configured-ami-package/)
* [在 *Fly.io* 上部署的教程](https://github.com/paulrudy/stmails-on-fly) -- 目前[不可用](https://github.com/knadh/stmails/issues/984#issuecomment-1694545255)
