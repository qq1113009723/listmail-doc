# 开发者设置
该应用程序有两个不同的组件：Go 后端和 VueJS 前端。在开发环境中，两者是独立运行的。

### 前提条件
- `go`
- `nodejs`（如果您正在开发前端）和 `yarn`
- PostgreSQL 数据库。如果没有本地安装，可以使用演示 docker 数据库进行开发（`docker compose up demo-db`）

### 首次设置
`git clone https://github.com/knadh/stmails.git`。该项目使用 go.mod，所以最好在 Go src 路径之外克隆它。

1. 将 `config.toml.sample` 复制为 `config.toml` 并添加您的配置。
2. 运行 `make dist` 构建 stmails 二进制文件。构建完成后，运行 `./stmails --install` 进行数据库设置。对于后续的开发运行，使用 `make run`。

> [mailhog](https://github.com/mailhog/MailHog) 是一个优秀的独立模拟 SMTP 服务器（带 UI），用于测试和开发。

### 运行开发环境
您可以在本地或容器内运行开发环境。

设置开发环境后，您可以访问 `http://localhost:8080`。

1. 本地运行
- 运行 `make run` 在 `:9000` 端口启动 stmails 开发服务器。
- 运行 `make run-frontend` 使用 yarn 在 `:8080` 端口以开发模式启动 Vue 前端。所有 `/api/*` 调用都被代理到运行在 `:9000` 端口的应用程序。有关前端结构的概述，请参阅[前端 README](https://github.com/knadh/stmails/blob/master/frontend/README.md)。

2. 容器内运行（使用 Makefile）
- 运行 `make init-dev-docker` 设置数据库容器。
- 运行 `make dev-docker` 设置 docker 容器套件。
- 运行 `make rm-dev-docker` 清理 docker 容器套件。

3. 容器内运行（使用 devcontainer）
- 在 vscode 中打开仓库，打开命令面板，选择"Dev Containers: Rebuild and Reopen in Container"。

它将设置数据库，并为您启动前端/后端。

### 生产构建
运行 `make dist` 构建 Go 二进制文件，构建 Javascript 前端，并嵌入静态资源，生成一个独立的二进制文件 `stmails`。
