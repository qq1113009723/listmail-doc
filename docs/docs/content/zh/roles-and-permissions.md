# 角色和权限

stmails（>= v4.0.0）支持创建具有各种功能细粒度权限的系统用户，包括特定列表的权限。用户可以使用用户名和密码登录，或者如果连接了身份验证提供程序，则可以通过 OIDC（OpenID Connect）握手登录。各种权限可以分组为"用户角色"，这些角色可以分配给用户。特定列表的权限可以分组为"列表角色"。

## 用户角色

用户角色是用户相关权限的集合。用户角色附加到用户账户。用户角色可以在 `管理 -> 用户 -> 用户角色` 中管理。权限说明如下。

| 组          | 权限                    | 说明                                                                                                                                                                                                                          |
| ----------- | ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| lists       | lists:get_all           | 获取所有列表的详细信息                                                                                                                                                                                                                |
|             | lists:manage_all        | 创建、更新和删除所有列表                                                                                                                                                                                                 |
| subscribers | subscribers:get         | 获取单个订阅者详细信息                                                                                                                                                                                                    |
|             | subscribers:get_all     | 获取所有订阅者及其详细信息                                                                                                                                                                                                |
|             | subscribers:manage      | 添加、更新和删除订阅者                                                                                                                                                                                                  |
|             | subscribers:import      | 从外部文件导入订阅者                                                                                                                                                                                               |
|             | subscribers:sql_query   | 对订阅者数据运行 SQL 查询。**警告：** 此权限将允许使用 SQL 表达式直接从数据库查询所有列表和订阅者，优先于上述单个列表和订阅者权限。 |
|             | tx:send                 | 向订阅者发送事务性消息                                                                                                                                                                                           |
| campaigns   | campaigns:get           | 获取活动详细信息                                                                                                                                                                                                                 |
|             | campaigns:get_analytics | 访问活动性能指标                                                                                                                                                                                                  |
|             | campaigns:manage        | 创建、更新和删除活动                                                                                                                                                                                                 |
| bounces     | bounces:get             | 获取电子邮件退信记录                                                                                                                                                                                                             |
|             | bounces:manage          | 处理退信电子邮件                                                                                                                                                                                                    |
|             | webhooks:post_bounce    | 通过 webhook 接收退信通知                                                                                                                                                                                             |
| media       | media:get               | 获取上传的媒体文件                                                                                                                                                                                                             |
|             | media:manage            | 上传、更新和删除媒体                                                                                                                                                                                                     |
| templates   | templates:get           | 获取电子邮件模板                                                                                                                                                                                                                  |
|             | templates:manage        | 创建、更新和删除模板                                                                                                                                                                                                 |
| users       | users:get               | 获取系统用户账户                                                                                                                                                                                                             |
|             | users:manage            | 创建、更新和删除用户账户                                                                                                                                                                                             |
|             | roles:get               | 获取用户角色和权限                                                                                                                                                                                                       |
|             | roles:manage            | 创建和修改用户角色                                                                                                                                                                                                         |
| settings    | settings:get            | 获取系统设置                                                                                                                                                                                                                  |
|             | settings:manage         | 修改系统配置                                                                                                                                                                                                          |
|             | settings:maintain       | 执行系统维护任务                                                                                                                                                                                                     |

## 列表角色

列表角色是每个列表分配的权限集合。每个列表可以被分配查看（读取）或管理（更新）权限。列表角色附加到用户账户。用户只能访问在列表角色中定义的列表，无论是在管理界面还是通过 API 调用。请注意，用户角色中的 `lists:get_all` 和 `lists:manage_all` 权限会覆盖所有每个列表的权限。

## API 用户

用户账户可以是两种类型之一：普通用户或 API 用户。API 用户用于以编程方式与 stmails API 交互。与具有自定义密码或 OIDC 身份验证的普通用户账户不同，API 用户会获得自动生成的密钥令牌。
