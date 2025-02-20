---
sidebar_position: 15
title: "SerpApi（通用搜索引擎 API）"
---

:::warning
本教程是来自社区的贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据您的具体需求来自定义 Open WebUI 的示例。想要参与贡献？请查看我们的贡献指南。
:::

## SerpApi API 配置

[SerpApi](https://serpapi.com/) 提供了一个快速、易用且功能完整的 API，可用于获取 Google 和其他搜索引擎的搜索结果。它支持所有能返回 `organic_results`（自然搜索结果）的现有或新增 SERP 引擎。系统默认使用 `google` 作为网页搜索引擎，但您也可以切换到 `bing`、`baidu`、`google_news`、`google_scholar`、`google_patents` 等其他引擎。

### 配置步骤

1. 访问 [SerpApi 官网](https://serpapi.com/)，登录您的账号或注册新账号。
2. 进入 `仪表板（Dashboard）` 页面并复制您的 API 密钥。
3. 获取 API 密钥后，打开 `Open WebUI 管理面板`，依次点击 `设置` 标签页和 `网页搜索` 选项。
4. 启用 `网页搜索` 功能，并在 `网页搜索引擎` 下拉菜单中选择 `serpapi`。
5. 将您在第 2 步从 [SerpApi 仪表板](https://serpapi.com/) 复制的 API 密钥填入 `SerpApi API 密钥` 字段。
6. [可选配置] 在 `SerpApi 引擎` 字段中输入您想要使用的搜索引擎名称。可选的引擎包括：`google`（默认）、`bing`、`baidu`、`google_news`、`google_videos`、`google_scholar` 和 `google_patents`。更多引擎选项请参考 [SerpApi 官方文档](https://serpapi.com/dashboard)。
7. 点击 `保存` 按钮完成配置。

![Open WebUI 管理面板配置界面](/images/tutorial_serpapi_search.png)

#### 使用说明

要使用 [SerpApi](https://serpapi.com/) 引擎进行网页搜索，您需要在对话输入框中启用 `网页搜索` 功能。

![网页搜索功能启用方式](/images/enable_web_search.png)
