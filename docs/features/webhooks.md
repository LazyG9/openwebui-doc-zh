---
sidebar_position: 17
title: "🪝 Webhook 集成"
---

概述
--------

Open WebUI 提供了 Webhook 功能，可以在新用户注册到您的实例时自动接收通知。您只需要向 Open WebUI 提供一个 Webhook URL，系统就会在新用户账号创建时向该 URL 发送通知。

在 Open WebUI 中配置 Webhook
---------------------------------

首先，您需要从支持 Webhook 的外部服务（如 Discord 频道或 Slack 工作区）获取一个 Webhook URL。这个 URL 将用于接收来自 Open WebUI 的通知。

配置 Webhook 有以下两种方式：

### 方式一：通过管理界面配置

1. 以管理员身份登录 Open WebUI
2. 进入`管理面板`
3. 点击顶部的`设置`标签
4. 在管理面板中找到`常规`配置部分
5. 定位到`Webhook URL`输入框
6. 输入您的 Webhook URL 并保存更改

### 方式二：通过环境变量配置

您也可以通过设置 `WEBHOOK_URL` 环境变量来配置 Webhook。关于环境变量的详细配置说明，请参阅[环境变量配置文档](https://docs.openwebui.com/getting-started/env-configuration/#webhook_url)。

### 验证配置

完成配置后，您可以通过以下步骤验证 Webhook 是否正常工作：
1. 在 Open WebUI 中创建一个新用户账号
2. 检查指定的 Webhook URL 是否收到通知
3. 确认通知内容是否正确

Webhook 数据格式
----------------------

Open WebUI 发送的 Webhook 数据采用纯文本格式，包含一条简单的新用户注册通知。数据格式如下：

```
New user signed up: <username>
```

示例：当用户"Tim"注册时，发送的数据为：

```
New user signed up: Tim
```

故障排除指南
--------------

如果 Webhook 未按预期工作，请检查以下几点：

* URL 相关：
  - Webhook URL 是否正确且格式无误
  - URL 是否可以正常访问
  
* 服务配置：
  - Webhook 服务是否已正确启用和配置
  - API 密钥（如果有）是否有效
  
* 连接问题：
  - 检查防火墙或代理设置
  - 验证网络连接是否正常
  - 确认 Webhook 服务器是否可用
  
* 日志检查：
  - 查看 Open WebUI 日志中的相关错误信息
  - 检查 Webhook 服务的响应日志

注意：Open WebUI 的 Webhook 功能正在持续优化中，我们计划在未来版本中添加更多功能和事件类型支持。如有建议，欢迎反馈。
