# 与外部系统集成

在许多环境中，邮件列表管理器的订阅者数据库并不是独立运行的，而是作为现有客户数据库或 CRM 系统的一部分。有多种方法可以保持 stmails 与外部系统的同步。

## 使用 API

[订阅者 API](apis/subscribers.md) 提供了多个用于操作订阅者数据库的 API，如添加、更新和删除。对于批量同步，可以生成 CSV 文件（可选择压缩）并通过导入 API 提交。

<!-- ## 直接与数据库交互

stmails 使用具有简单架构的表来表示订阅者（`subscribers`）、列表（`lists`）和订阅关系（`subscriber_lists`）。对于高级用例，可以直接通过数据库表添加、更新和删除订阅者信息。有关更多信息，请参阅[表结构](https://github.com/knadh/stmails/blob/master/schema.sql)。 -->
