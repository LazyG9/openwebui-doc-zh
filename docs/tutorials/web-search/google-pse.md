---
sidebar_position: 5
title: "Google PSE（Google 可编程搜索引擎）"
---

:::warning
本教程是来自社区的贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据您的具体需求来自定义 Open WebUI 的示例。想要参与贡献？请查看我们的贡献指南。
:::

## Google PSE API 配置

### 配置步骤

1. 访问 Google Developers 平台，打开 [可编程搜索引擎（Programmable Search Engine）](https://developers.google.com/custom-search) 页面，登录您的 Google 账号或创建新账号。
2. 进入[控制面板](https://programmablesearchengine.google.com/controlpanel/all)，点击 `添加（Add）` 按钮。
3. 输入搜索引擎名称，根据您的需求配置其他选项，完成人机验证后点击 `创建（Create）` 按钮。
4. 生成 `API 密钥` 并获取 `搜索引擎 ID`（这些信息在搜索引擎创建完成后才可获取）。
5. 获取 API 密钥和搜索引擎 ID 后，打开 `Open WebUI 管理面板`，依次点击 `设置` 标签页和 `网页搜索` 选项。
6. 启用 `网页搜索` 功能，并在 `网页搜索引擎` 下拉菜单中选择 `google_pse`。
7. 将您在第 4 步获取的 `API 密钥` 和 `搜索引擎 ID` 分别填入 `Google PSE API 密钥` 和 `Google PSE 引擎 ID` 字段。
8. 点击 `保存` 按钮完成配置。

![Open WebUI 管理面板配置界面](/images/tutorial_google_pse1.png)

#### 使用说明

要使用 Google PSE 进行网页搜索，您需要点击对话输入框旁的加号（`+`）按钮来启用 `网页搜索` 功能。
现在您可以开始使用网页搜索功能了 ;-)

![网页搜索功能启用方式](/images/tutorial_google_pse2.png)
