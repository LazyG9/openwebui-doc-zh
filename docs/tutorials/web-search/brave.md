---
sidebar_position: 2
title: "Brave（Brave 搜索引擎）"
---

:::warning
本教程是来自社区的贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据您的具体需求来自定义 Open WebUI 的示例。想要参与贡献？请查看我们的贡献指南。
:::

## Brave API 配置

### Docker Compose 配置步骤

请将以下环境变量添加到您的 Open WebUI `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      # 启用 RAG 网页搜索功能
      ENABLE_RAG_WEB_SEARCH: True
      # 设置搜索引擎为 Brave
      RAG_WEB_SEARCH_ENGINE: "brave"
      # 设置您的 Brave 搜索 API 密钥
      BRAVE_SEARCH_API_KEY: "YOUR_API_KEY"
      # 设置每次搜索返回的结果数量
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      # 设置并发请求数量
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
```
