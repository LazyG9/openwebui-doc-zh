---
sidebar_position: 8
title: "Mojeek（Mojeek 搜索引擎）"
---

:::warning
本教程是来自社区的贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据您的具体需求来自定义 Open WebUI 的示例。想要参与贡献？请查看我们的贡献指南。
:::

## Mojeek 搜索 API 配置

### 基础设置步骤

1. 请访问 [Mojeek 搜索 API 页面](https://www.mojeek.com/services/search/web-search-api/) 申请并获取您的 `API 密钥`
2. 获得 `API 密钥` 后，打开 `Open WebUI 管理面板`，依次点击 `设置` 标签页和 `网页搜索` 选项
3. 启用 `网页搜索` 功能，并在 `网页搜索引擎` 下拉菜单中选择 `mojeek`
4. 将您获取的 `API 密钥` 填入 `Mojeek 搜索 API 密钥` 字段
5. 点击 `保存` 按钮完成配置

### Docker Compose 配置步骤

请将以下环境变量添加到您的 Open WebUI `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      # 启用 RAG 网页搜索功能
      ENABLE_RAG_WEB_SEARCH: True
      # 设置搜索引擎为 Mojeek
      RAG_WEB_SEARCH_ENGINE: "mojeek"
      # 设置您的 Mojeek 搜索 API 密钥
      MOJEEK_SEARCH_API_KEY: "YOUR_MOJEEK_API_KEY"
      # 设置每次搜索返回的结果数量
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      # 设置并发请求数量
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
```
