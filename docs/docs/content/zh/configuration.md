# 配置

### TOML 配置文件
可以通过多次传递 `--config config.toml` 来读取一个或多个 TOML 文件。除了少数低级配置变量和数据库配置外，所有其他设置都可以从管理界面的 `设置` 仪表板中管理。

要生成新的示例配置文件，请运行 `--stmails --new-config`

### 环境变量
config.toml 中的变量也可以通过环境变量提供，环境变量以 `stmails_` 为前缀，点号替换为 `__`（双下划线）。要仅使用环境变量而不使用配置文件启动 stmails，请设置环境变量并将配置标志传递为 `--config=""`。

示例：

| **环境变量**       | 示例值  |
| ------------------------------ | -------------- |
| `stmails_app__address`        | "0.0.0.0:9000" |
| `stmails_db__host`            | db             |
| `stmails_db__port`            | 9432           |
| `stmails_db__user`            | stmails       |
| `stmails_db__password`        | stmails       |
| `stmails_db__database`        | stmails       |
| `stmails_db__ssl_mode`        | disable        |

### 自定义系统模板
请参阅[系统模板](templating.md#system-templates)。

### HTTP 路由
在配置身份验证代理和 Web 应用程序防火墙时，请使用此表。

#### 私有管理端点。

| 方法 | 路由              | 说明             |
| ------- | ------------------ | ----------------------- |
| `*`     | `/api/*`           | 管理 API              |
| `GET`   | `/admin/*`         | 管理界面和 HTML 页面 |
| `POST`  | `/webhooks/bounce` | 管理退信 webhook    |

#### 暴露给互联网的公共端点。

| 方法     | 路由                 | 说明                                   |
| ----------- | --------------------- | --------------------------------------------- |
| `GET, POST` | `/subscription/*`     | HTML 订阅页面                       |
| `GET, `     | `/link/*`             | 跟踪链接重定向                      |
| `GET`       | `/campaign/*`         | 像素跟踪图像                          |
| `GET`       | `/public/*`           | HTML 订阅页面的静态文件      |
| `POST`      | `/webhooks/service/*` | AWS 和 Sendgrid 的退信 webhook 端点 |
| `GET`       | `/uploads/*`          | 媒体设置中配置的文件上传路径 |

## 媒体上传

#### 使用文件系统

在配置 `docker` 卷挂载以使用文件系统媒体上传时，您可以采用以下两种方法之一。[如果您需要使用 `sudo` 执行 docker 命令，第二种选项可能是必要的](https://github.com/knadh/stmails/issues/1169#issuecomment-1674475945)。

进行任何更改后，您需要运行 `sudo docker compose stop ; sudo docker compose up`。

在 `https://stmails.mysite.com/admin/settings` 下，您输入 `/stmails/uploads`。

#### 使用卷

使用 `docker volumes`，您可以指定卷的名称和容器内要上传的文件的目标位置。

```yml
app:
    volumes:
      - type: volume
        source: stmails-uploads
        target: /stmails/uploads

volumes:
  stmails-uploads:
```

!!! 注意

    此卷由 `docker` 本身管理，您可以使用 `docker volume inspect stmails_stmails-uploads` 查看主机路径。

#### 使用绑定挂载

```yml
  app:
    volumes:
      - ./path/on/your/host/:/path/inside/container
```
例如：
```yml
  app:
    volumes:
      - ./data/uploads:/stmails/uploads
```
文件将在主机上的 `/data/uploads` 目录中可用。

要使用默认的 `uploads` 文件夹：
```yml
  app:
    volumes:
      - ./uploads:/stmails/uploads
```

## 日志

### Docker

https://docs.docker.com/engine/reference/commandline/logs/
```
sudo docker logs -f
sudo docker logs stmails_app -t
sudo docker logs stmails_db -t
sudo docker logs --help
```
容器信息：`sudo docker inspect stmails_stmails`

Docker 将日志输出到 `/dev/stdout` 和 `/dev/stderr`。日志由 docker 守护进程收集并存储在您节点的主机路径中（默认情况下）。可以在 docker 守护进程设置中配置相同的设置（/etc/docker/daemon.json）以设置其他日志驱动程序、logrotate 策略等，您可以[在此处](https://docs.docker.com/config/containers/logging/configure/)阅读更多信息。

### 二进制

stmails 将日志输出到 `stdout`，通常不会保存到任何文件中。要将 stmails 日志保存到文件中，请使用 `./stmails > stmails.log`。

管理界面中的设置 -> 日志显示标准日志输出的最后 1000 行，但在 stmails 重启时会被清除。

对于[服务文件](https://github.com/knadh/stmails/blob/master/stmails%40.service)，您可以使用 `ExecStart=/bin/bash -ce "exec /usr/bin/stmails --config /etc/stmails/config.toml --static-dir /etc/stmails/static >>/etc/stmails/stmails.log 2>&1"` 创建一个在重启后仍然存在的日志文件。[更多信息](https://github.com/knadh/stmails/issues/1462#issuecomment-1868501606)。

## 时区

要更改 stmails 的时区（日志等），请编辑 `docker-compose.yml`：
```
environment:
    - TZ=Etc/UTC
```
使用[此处](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)列出的任何时区。然后运行 `sudo docker-compose stop ; sudo docker-compose up`。

## SMTP

### 重试
`设置 -> SMTP -> 重试` 表示在发送时失败的消息使用 SMTP 池中的不同连接静默重试的次数。即使在重试后仍然失败的消息会被记录为错误并被忽略。

## SMTP 端口
某些服务器主机会阻止出站 SMTP 端口（25、465）。您可能需要联系您的主机以解除阻止，然后才能发送电子邮件。例如：[Hetzner](https://docs.hetzner.com/cloud/servers/faq/#why-can-i-not-send-any-mails-from-my-server)。

## 性能

### 批处理大小

批处理大小参数在处理具有数百万订阅者的非常大的列表时非常有用，可以最大化吞吐量。它是在活动运行时从数据库中顺序获取的订阅者数量（约 5 秒一个周期）。增加批处理大小会使用更多内存，但会减少到数据库的往返次数。
