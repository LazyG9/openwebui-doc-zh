---
sidebar_position: 6
title: "📊 系统监控"
---

# Open WebUI 监控指南

对 Open WebUI 实例进行监控对于保障服务稳定性和快速发现问题至关重要。本指南将从以下三个层面详细介绍监控方案：
- 基础健康检查：确保服务基本可用性
- 模型连接性检查：验证与模型服务的连接状态
- 深度健康检查：包含实际的模型响应测试

## 基础健康检查接口

Open WebUI 提供了一个位于 `/health` 路径的健康检查接口，当服务正常运行时会返回 HTTP 200 OK 状态码。

```bash
# 注意：此接口无需认证即可访问
curl https://您的服务域名/health
```

### 配置 Uptime Kuma 监控
[Uptime Kuma](https://github.com/louislam/uptime-kuma) 是一款优秀的开源监控工具，具有易用、轻量级和自托管等特点。

1. 在 Uptime Kuma 控制面板中，选择"添加新监控"
2. 配置监控项：
   - 监控类型：选择 HTTP(s)
   - 监控名称：Open WebUI 健康状态
   - 监控地址：`http://您的服务地址:8080/health`
   - 检查频率：60秒（可根据需求调整）
   - 失败重试：建议设置为 3 次

此健康检查将验证以下内容：
- Web 服务是否正常响应
- 应用程序是否正常运行
- 数据库连接是否正常

## 模型服务连接性监控

如需验证 Open WebUI 是否能够正常连接并获取已配置的模型列表，您可以通过监控模型接口来实现。此接口需要进行身份认证，用于检查 Open WebUI 与模型服务提供商之间的通信状态。

详细的接口说明请参考 [API 文档](https://docs.openwebui.com/getting-started/api-endpoints/#-retrieve-all-models)。

```bash
# 使用 API 密钥进行认证（获取方式见下文）
curl -H "Authorization: Bearer sk-your-api-key" https://您的服务域名/api/models
```

### API 认证配置

1. 开启 API 密钥功能（需要管理员权限）：
   - 访问：管理员设置 > 常规设置
   - 启用：API 密钥功能
   - 保存配置

2. 获取 API 密钥（参考[详细文档](https://docs.openwebui.com/getting-started/api-endpoints)）：
   - 建议：创建专门用于监控的非管理员账号
   - 进入：设置 > 账户管理
   - 生成：新的监控专用 API 密钥
   - 保存：将密钥安全保存以供后续使用

注意：如果您在账户设置中未看到 API 密钥选项，请联系系统管理员确认是否已启用该功能。

### Uptime Kuma 模型连接性监控配置

1. 创建新的监控项：
   - 监控类型：HTTP(s) 带 JSON 查询
   - 监控名称：Open WebUI 模型服务状态
   - 监控地址：`http://您的服务地址:8080/api/models`
   - 请求方法：GET
   - 期望状态码：200
   - JSON 查询表达式：`$count(data[*])>0`
   - 期望结果：`true`
   - 检查间隔：建议 300 秒（5分钟）

2. 配置认证信息：
   - 在请求头部分添加：
     ```
     {
        "Authorization": "Bearer 您的API密钥"
     }
     ```
   - 注意替换为您实际的 API 密钥

JSON 查询示例（可根据需求选择）：
```
# 检查是否至少有一个 ollama 提供的模型
$count(data[owned_by='ollama'])>1

# 检查特定模型是否可用
$exists(data[id='gpt-4o'])

# 同时检查多个指定模型是否都可用
$count(data[id in ['gpt-4o', 'gpt-4o-mini']]) = 2
```

您可以使用 [jsonata.org](https://try.jsonata.org/) 在线工具验证您的 JSON 查询表达式。

## 模型响应能力测试

为了验证模型是否能够正常处理和响应请求，您可以监控对话完成接口。这种方式通过实际测试模型的响应能力，提供了更深层次的健康状态检查。

```bash
# 发送测试请求到模型服务
curl -X POST https://您的服务域名/api/chat/completions \
  -H "Authorization: Bearer 您的API密钥" \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [{"role": "user", "content": "请回复 HEALTHY 一词"}],
    "model": "llama3.1",
    "temperature": 0
  }'
```

