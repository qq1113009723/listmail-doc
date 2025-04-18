# 存档

在公共web界面上维护着一个全局公共存档。你可以在 “设置” -> “设置” -> “常规” -> 
“启用公共邮件列表存档” 选项下启用它。

要使某个营销活动在公共存档中可见（前提是已按上述描述在设置中启用了公共存档功能），
请在 “营销活动” -> “新建” -> “存档” 下启用 “发布到公共存档” 选项。

当使用依赖于订阅用户数据的模板变量时（例如任何引用 `.Subscriber` 的模板变量），
此类数据必须作为 “营销活动元数据” 提供，这是一个 JSON 对象，
在渲染存档模板和内容时，它将用来替代 `.Subscriber`

当启用了对单个订阅用户的追踪功能时， “追踪链接（TrackLink）” 要求将现有用户的通用
唯一识别码（UUID）作为营销活动元数据的一部分提供。来自已存档营销活动的追踪链接的任何点击操作，
都将记录到该订阅用户的点击数据中。


举例如下：


```json
{
  "UUID": "5a837423-a186-5623-9a87-82691cbe3631",
  "email": "example@example.com",
  "name": "Reader",
  "attribs": {}
}
```

![Archive campaign](../images/archived-campaign-metadata.png)

