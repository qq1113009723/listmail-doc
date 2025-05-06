# 退信处理

在设置 -> 退信中启用退信处理。POP3 退信扫描和 API 只有在启用此设置后才会可用。

## POP3 退信邮箱
在设置 -> 退信中配置退信邮箱。活动（或在设置中）设置的"发件人"电子邮件地址应该有一个 POP3 邮箱来接收退信邮件，或者您应该配置一个专用的 POP3 邮箱，并在设置 -> SMTP -> 自定义头部框中将该地址添加为 `Return-Path`（信封发件人）头部。例如：

```
[
	{"Return-Path": "your-bounce-inbox@site.com"}
]

```

某些邮件服务器也可能将退信返回到 `Reply-To` 地址，这也可以添加到头部设置中。

## Webhook API
退信 webhook API 可用于通过自定义脚本记录退信事件。这可以通过读取邮箱、数据库或邮件服务器日志来实现。

| 方法 | 端点         | 说明            |
| ------ | ---------------- | ---------------------- |
| `POST` | /webhooks/bounce | 记录退信事件。 |


| 名称            | 类型      | 必填   | 说明                                                                          |
| ----------------| --------- | -----------| ------------------------------------------------------------------------------------ |
| subscriber_uuid | string    |            | 订阅者的 UUID。需要提供此字段或 `email`。                      |
| email           | string    |            | 订阅者的电子邮件。需要提供此字段或 `subscriber_uuid`。          |
| campaign_uuid   | string    |            | 发生退信的活动 UUID。                                  |
| source          | string    | 是        | 表示来源的字符串，例如：`api`、`my_script` 等。                          |
| type            | string    | 是        | `hard` 或 `soft` 退信。目前，这不会影响退信的处理方式。 |
| meta            | string    |            | 可选的转义 JSON 字符串，包含关于退信事件的任意元数据。      |
 

```shell
curl -u 'api_username:access_token' -X POST 'http://localhost:9000/webhooks/bounce' \
	-H "Content-Type: application/json" \
	--data '{"email": "user1@mail.com", "campaign_uuid": "9f86b50d-5711-41c8-ab03-bc91c43d711b", "source": "api", "type": "hard", "meta": "{\"additional\": \"info\"}}'

```

## 外部 webhooks
stmails 支持从以下 SMTP 提供商接收退信 webhook 事件。

| 端点                                                      | 说明                            | 更多信息                                                                                                             |
|:--------------------------------------------------------------|:---------------------------------------|:----------------------------------------------------------------------------------------------------------------------|
| `https://stmails.yoursite.com/webhooks/service/ses`          | Amazon (AWS) SES                       | 见下文                                                                                                             |
| `https://stmails.yoursite.com/webhooks/service/sendgrid`     | Sendgrid / Twilio 签名事件 webhook | [更多信息](https://docs.sendgrid.com/for-developers/tracking-events/getting-started-event-webhook-security-features) |
| `https://stmails.yoursite.com/webhooks/service/postmark`     | Postmark webhook                       | [更多信息](https://postmarkapp.com/developer/webhooks/webhooks-overview)                                             |
| `https://stmails.yoursite.com/webhooks/service/forwardemail` | Forward Email webhook                   | [更多信息](https://forwardemail.net/en/faq#do-you-support-bounce-webhooks)                                                  |

## Amazon Simple Email Service (SES)

如果使用 SES 作为您的 SMTP 提供商，自动退信处理是维护您的[发件人声誉](https://docs.aws.amazon.com/ses/latest/dg/monitor-sender-reputation.html)的推荐方式。以下设置基于 Amazon 的[建议](https://docs.aws.amazon.com/ses/latest/dg/send-email-concepts-deliverability.html)。请注意，在继续之前，您的发送域名必须在 SES 中验证。

1. 在 stmails 设置中，转到"退信"选项卡并配置以下内容：
    - 启用退信处理：`已启用`
        - 软退信：
            - 退信计数：`2`
            - 操作：`无`
        - 硬退信：
            - 退信计数：`1`
            - 操作：`加入黑名单`
        - 投诉： 
            - 退信计数：`1`
            - 操作：`加入黑名单`
    - 启用退信 webhooks：`已启用`
    - 启用 SES：`已启用`
2. 在 AWS 控制台中，转到[Simple Notification Service](https://console.aws.amazon.com/sns/)并创建一个新主题，设置如下：
    - 类型：`标准`
    - 名称：`ses-bounces`（或任何其他名称）
3. 为该主题创建新订阅，设置如下：
    - 协议：`HTTPS`
    - 端点：`https://stmails.yoursite.com/webhooks/service/ses`
    - 启用原始消息传递：`已禁用`（未选中）
4. SES 将向您的 stmails 实例发送请求以确认订阅。刷新页面后，订阅状态应为"已确认"。如果不是，您的端点可能不正确或无法公开访问。
5. 在 AWS 控制台中，转到[Simple Email Service](https://console.aws.amazon.com/ses/)并点击左侧边栏中的"已验证身份"。
6. 点击您的域名并转到"通知"选项卡。
7. 在"反馈通知"旁边，点击"编辑"。
8. 对于"退信反馈"和"投诉反馈"，使用以下设置：
    - SNS 主题：`ses-bounces`（或您命名的任何名称）
    - 包含原始电子邮件头部：`已启用`（选中）
9. 对于使用 stmails 发送的每个`电子邮件地址`身份，重复步骤 6-8
10. 退信处理现在应该可以工作了。您可以使用[SES 模拟器地址](https://docs.aws.amazon.com/ses/latest/dg/send-an-email-from-console.html#send-email-simulator)进行测试。将它们添加为订阅者，发送活动预览，并确保在达到配置的退信计数后采取了适当的操作。
    - 软退信：`ooto@simulator.amazonses.com`
    - 硬退信：`bounce@simulator.amazonses.com`
    - 投诉：`complaint@simulator.amazonses.com`
11. 您可以选择[禁用电子邮件反馈转发](https://docs.aws.amazon.com/ses/latest/dg/monitor-sending-activity-using-notifications-email.html#monitor-sending-activity-using-notifications-email-disabling)。

## 导出退信

可以通过 JSON API 导出退信：
```shell
curl -u 'username:passsword' 'http://localhost:9000/api/bounces'
```

或直接查询数据库：
```sql
SELECT bounces.created_at,
    bounces.subscriber_id,
    subscribers.uuid AS subscriber_uuid,
    subscribers.email AS email
FROM bounces
LEFT JOIN subscribers ON (subscribers.id = bounces.subscriber_id)
ORDER BY bounces.created_at DESC LIMIT 1000;
```