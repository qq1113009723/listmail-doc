# 概念

## 订阅者

订阅者是指通过电子邮件地址和姓名识别的接收者。订阅者会收到从 listmail 发送的电子邮件。一个订阅者可以添加到任意数量的列表中。未加入任何列表的订阅者被视为孤立记录。
### 属性

属性是附加在订阅者身上的任意属性，除了电子邮件和姓名之外。它们还以 JSON 映射的形式表示。并非所有订阅者都需要具有属性。可以根据订阅者的属性查询和分段到不同的列表中，并将属性插入发送给他们的电子邮件中。例如：
```json
{
  "city": "Bengaluru",
  "likes_tea": true,
  "spoken_languages": ["English", "Malayalam"],
  "projects": 3,
  "stack": {
    "frameworks": ["echo", "go"],
    "languages": ["go", "python"],
    "preferred_language": "go"
  }
}
```

### 订阅状态

订阅者可以被添加到一个或多个列表中，每个这样的关系可以具有这些状态之一。

| 状态             | 说明                                                                                                                                                                                          |
|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `unconfirmed`  | 订阅者是在没有获得其明确确认的情况下直接被添加到列表中的。尽管如此，该订阅者仍会收到发送给 “单次确认式” 营销活动的营销活动信息。                                                                                                                          |
| `confirmed`    | 订阅者通过点击确认电子邮件中的 “接受” 按钮来确认了他们的订阅。只有在选择加入列表中已确认的订阅者才会收到发送到该列表的营销活动信息。 |
| `unsubscribed` | 该订阅者已从列表中退订，将不会收到发送至该列表的任何营销活动信息。                                                                                   


### 分段

分段是指根据任意条件（主要基于订阅者的属性），将大量的订阅者列表筛选成一个较小群体的过程。例如，如果需要向居住在某个特定城市的订阅者发送电子邮件（假设他们所在城市的信息在其属性中有描述)，那么就可以快速地将这些订阅者筛选出来，组成一个新的列表，然后向他们发送邮件。 [Learn more](querying-and-segmentation.md).

## 列表

列表（或 “邮件列表”）是指一群订阅者的集合，这些订阅者归在一个名称之下，例如 “客户”。列表用于对订阅者进行组织管理，以及向特定的群体发送电子邮件。一个列表可以是 “单次确认式” 或 “双次确认式” 的。被添加到 “双次确认式” 列表中的订阅者必须通过点击他们所收到的确认电子邮件来明确接受订阅。在确认之前，他们不会收到营销活动的邮件信息。
## 营销活动

营销活动是指发送给一个或多个邮件列表的一封电子邮件（或任何其他类型的信息）。


[//]: # (## 事物性消息)

[//]: # ()
[//]: # (事务性消息是通过事务性消息应用程序编程接口（API）发送给订阅者的任意一种消息。例如，当用户注册一项服务时发送的欢迎电子邮件；用户购买商品时发送的订单确认电子邮件；当用户启动在线账户找回流程时发送的密码重置电子邮件。)

## 模板

模板是一种可重复使用的（HTML）设计，可用于各类营销活动。通常情况下，模板包含徽标和品牌元素的标准页眉和页脚区域，而营销活动的内容则插入到中间部分。listmail 支持您能够创建功能强大、动态的 HTML 模板 [Learn more](templating.md).

## Messenger

listmail supports multiple custom messaging backends in additional to the default SMTP e-mail backend, enabling not just e-mail campaigns, but arbitrary message campaigns such as SMS, FCM notifications etc. A *Messenger* is a web service that accepts a campaign message pushed to it as a JSON request, which the service can in turn broadcast as SMS, FCM etc. [Learn more](messengers.md).

## Tracking pixel

The tracking pixel is a tiny, invisible image that is inserted into an e-mail body to track e-mail views. This allows measuring the read rate of e-mails. While this is exceedingly common in e-mail campaigns, it carries privacy implications and should be used in compliance with rules and regulations such as GDPR. It is possible to track reads anonymously without associating an e-mail read to a subscriber.

## Click tracking

It is possible to track the clicks on every link that is sent in an e-mail. This allows measuring the clickthrough rates of links in e-mails. While this is exceedingly common in e-mail campaigns, it carries privacy implications and should be used in compliance with rules and regulations such as GDPR. It is possible to track link clicks anonymously without associating an e-mail read to a subscriber.

## Bounce

A bounce occurs when an e-mail that is sent to a recipient "bounces" back for one of many reasons including the recipient address being invalid, their mailbox being full, or the recipient's e-mail service provider marking the e-mail as spam. listmail can automatically process such bounce e-mails that land in a configured POP mailbox, or via APIs of SMTP e-mail providers such as AWS SES and Sengrid. Based on settings, subscribers returning bounced e-mails can either be blocklisted or deleted automatically. [Learn more](bounces.md).
