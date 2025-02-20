---
sidebar_position: 1
title: "Bing（必应搜索）"
---

:::warning
本教程是来自社区的贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据您的具体需求来自定义 Open WebUI 的示例。想要参与贡献？请查看我们的贡献指南。
:::

## Bing API 配置

### 设置步骤

1. 访问 [Azure 门户](https://portal.azure.com/#create/Microsoft.BingSearch) 并创建一个新的资源。创建完成后，系统会自动跳转到资源概览页面。在页面中找到并点击"点击此处管理密钥"选项。![点击此处管理密钥](https://github.com/user-attachments/assets/dd2a3c67-d6a7-4198-ba54-67a3c8acff6d)
2. 在密钥管理页面上，找到密钥1（Key1）或密钥2（Key2），并复制您要使用的密钥。
3. 打开 Open WebUI 管理面板，切换到"设置"标签页，然后选择"网页搜索"选项。
4. 启用"网页搜索"功能，并在搜索引擎选项中选择"Bing"。
5. 将您在第 2 步从 [Azure 门户](https://portal.azure.com/#create/Microsoft.BingSearch) 仪表板复制的密钥填入 `SearchApi API Key` 字段。
6. 点击"保存"按钮完成设置。
