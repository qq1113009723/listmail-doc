# 消息发送器

除了默认的 SMTP 电子邮件后端外，stmails 还支持多个自定义消息发送后端，不仅支持电子邮件活动，还支持任意消息活动，如 SMS、FCM 通知等。

*消息发送器*是一个 Web 服务，它接受作为 JSON 请求推送的活动消息，该服务可以将其作为 SMS、FCM 等广播。消息发送器在*设置 -> 消息发送器* UI 中注册，可以在各个活动中选择。

消息发送器支持可选的 BasicAuth 身份验证。对于 SMS 和 FCM 等消息发送器，活动内容的`纯文本`格式是理想的选择。

当活动开始时，stmails 会以以下格式向选定的消息发送器端点发送 POST 请求。如果请求成功，端点应返回 `200 OK` 响应。

广播消息所需的地址，例如电话号码或 FCM ID，应该作为[订阅者属性](concepts.md/#attributes)存储和传递。

```json
{
	"subject": "Welcome to stmails",
	"body": "The message body",
	"content_type": "plain",
	"recipients": [{
		"uuid": "e44b4135-1e1d-40c5-8a30-0f9a886c2884",
		"email": "anon@example.com",
		"name": "Anon Doe",
		"attribs": {
			"phone": "123123123",
			"fcm_id": "2e7e4b512e7e4b512e7e4b51",
			"city": "Bengaluru"
		},
		"status": "enabled"
	}],
	"campaign": {
		"uuid": "2e7e4b51-f31b-418a-a120-e41800cb689f",
		"name": "Test campaign",
		"tags": ["test-campaign"]
	}
}
```

## 消息发送器实现

以下是连接到各种后端的 HTTP 消息发送器服务器列表。

| 名称                                                                                 | 后端          |
|:-------------------------------------------------------------------------------------|:-----------------|
| [stmails-messenger](https://github.com/joeirimpan/stmails-messenger)               | AWS Pinpoint SMS |
| [stmails-verimor-gateway](https://github.com/antandros/stmails-verimor-gateway)    | Verimor          |
| [stmails-mailersend](https://github.com/tkawczynski/stmails-mailersend)            | Mailersend       |
| [stmails-novu-messenger](https://github.com/Codepowercode/stmails-novu-messenger)  | Novu             |
| [stmails-push-messenger](https://github.com/shyamkrishna21/stmails-push-messenger) | Google FCM       |
