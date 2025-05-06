# 升级

!!! 警告
    在升级 stmails 之前，请始终备份 PostgreSQL 数据库

## 二进制安装
- 停止正在运行的 stmails 实例。
- 下载[最新版本](https://github.com/knadh/stmails/releases)并解压 stmails 二进制文件，覆盖之前的版本。
- 运行 `./stmails --upgrade` 升级现有数据库架构。升级是幂等的，多次运行不会有副作用。
- 再次运行 `./stmails`。

如果您将 stmails 安装为服务，您需要在覆盖二进制文件之前停止它。可以使用 `sudo systemctl stop stmails` 或 `sudo service stmails stop` 等命令。然后覆盖二进制文件为新版本，运行 `./stmails --upgrade`，并使用相同的命令重新"启动"它。

如果不是作为服务运行，可以使用 `pkill -9 stmails` 停止 stmails 进程。

## Docker
**重要：** 以下说明适用于新的 [docker-compose.yml](https://github.com/knadh/stmails/blob/master/docker-compose.yml) 文件。

```shell
docker compose down app
docker compose pull
docker compose up app -d
```

如果您使用的是较旧的 docker-compose.yml 文件，您需要手动运行 `--upgrade` 步骤。

```shell
docker-compose down
docker-compose pull && docker-compose run --rm app ./stmails --upgrade
docker-compose up -d app db
```

## Railway
- 进入您的仪表板，选择您的 stmails 项目。
- 选择 GitHub 部署服务。
- 在部署选项卡中，进入最新部署，点击右侧的三个垂直点，选择"重新部署"。

![Railway 重新部署选项](https://user-images.githubusercontent.com/55474996/226517149-6dc512d5-f862-46f7-a57d-5e55b781ff53.png)

## 降级

要恢复到之前的版本，您必须恢复该特定版本的数据库。已使用特定版本升级的数据库不应与旧版本一起使用。新版本可能带来与旧版本不兼容的数据库更改。

**一般步骤：**

1. 停止 stmails。
2. 恢复升级前的数据库。
3. 如果您使用 `docker compose`，编辑 `docker-compose.yml` 并将 `stmails:latest` 更改为 `stmails:v2.4.0`（例如）。
4. 重新启动。

**Docker 示例：**

1. 停止 stmails（app）：
```
sudo docker stop stmails_app
```
2. 恢复升级前的数据库（必需）（注意，这将清除您现有的数据库）：
```
psql -h 127.0.0.1 -p 9432 -U stmails
drop schema public cascade;
create schema public;
\q
psql -h 127.0.0.1 -p 9432 -U stmails -W stmails < stmails-preupgrade-db.sql
```
3. 编辑 `docker-compose.yml`：
```
x-app-defaults: &app-defaults
  restart: unless-stopped
  image: stmails/stmails:v2.4.0
```
4. 重新启动：
`sudo docker compose up -d app db nginx certbot`

## 升级到 v4.x.x
v4 是从之前版本的重大升级，对某些重要功能和行为进行了重大更改。这是第一个具有多用户支持和完整用户管理的版本。之前的版本只对管理员登录（浏览器提示）和 API 调用使用简单的 BasicAuth，用户名和密码在 TOML 配置文件中定义。

使用 `--upgrade` 升级旧安装是安全的，但有一些重要事项需要注意。升级会自动将 TOML 配置中定义的 `admin_username` 和 `admin_password` 导入到新的用户管理系统中。

1. **新的登录界面**：一旦您升级旧安装，管理员仪表板将不再显示原生的浏览器登录提示。相反，stmails 渲染的新登录界面将显示在 URI `/admin/login` 处。

1. **API 凭证**：如果您使用 API 与 stmails 交互，登录后，转到设置 -> 用户并创建一个具有必要权限的新 API 用户。更改现有的 API 集成以使用这些凭证，而不是旧版 TOML 配置文件或环境变量中定义的用户名和密码。

1. **TOML 文件或旧环境变量中的凭证**：在从配置文件或旧环境变量中删除 `admin_username` 和 `admin_password` 字段之前，管理员仪表板会显示警告。在 v4.x.x 中，这些字段无关紧要，因为用户凭证存储在数据库中并从管理界面管理。重要：如果您使用 API 与 stmails 交互，请在删除旧版凭证之前执行上一步。
