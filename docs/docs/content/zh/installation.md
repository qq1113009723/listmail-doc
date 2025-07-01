# 安装

stmails 是一个简单的二进制应用程序，可以手动下载并运行二进制文件。

## 二进制安装
1. 下载[最新版本](https://www.stmails.cn)并解压 stmails 二进制文件。`amd64` 是主要版本，适用于 Intel 和 x86 CPU。
2. **重要：** 在 config.toml 文件中设置您的许可证密钥。这是运行应用程序的必要步骤。
   ```toml
   [app]
   license_key = "您的许可证密钥"
   ```
3. 运行 `./stmails` 并访问 `http://localhost:9000` 创建超级管理员用户并登录。