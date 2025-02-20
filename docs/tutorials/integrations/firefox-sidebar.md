---
sidebar_position: 4100
title: "🦊 Firefox AI 智能助手侧边栏"
---

:::warning
注意：本教程来自社区贡献，不由 Open WebUI 团队官方支持。它仅用于演示如何根据具体需求自定义 Open WebUI。如果您也想为社区做出贡献，欢迎参考贡献指南。
:::

## 🦊 Firefox AI 智能助手侧边栏

# 在 Mozilla Firefox 中集成 Open WebUI 作为本地 AI 智能助手

目录
=================
1. [环境准备](#prerequisites)
2. [启用 Firefox AI 智能助手](#enabling-ai-chatbot-in-firefox)
3. [高级配置设置](#configuring-aboutconfig-settings)
    * [启用聊天功能](#browsermlchatenabled)
    * [显示本地服务](#browsermlchathidelocalhost)
    * [自定义提示词](#browsermlchatsprompts)
    * [服务提供者](#browsermlchatprovider)
4. [URL 参数配置](#url-parameters-for-open-webui)
    * [AI 模型设置](#models-and-model-selection)
    * [YouTube 视频转录](#youtube-transcription)
    * [网页搜索集成](#web-search)
    * [工具选择](#tool-selection)
    * [视频通话](#call-overlay)
    * [默认提示词](#initial-query-prompt)
    * [临时会话](#temporary-chat-sessions)
5. [扩展配置选项](#additional-aboutconfig-settings)
6. [快速访问方式](#accessing-the-ai-chatbot-sidebar)

## 环境准备

在开始集成之前，请确保您已经准备好：

* Open WebUI 服务地址（可以是本地或远程地址）
* 已安装最新版本的 Firefox 浏览器

## 启用 Firefox AI 智能助手

1. 点击 Firefox 右上角的菜单按钮（三横线图标，位于关闭按钮下方）
2. 进入 Firefox 设置界面
3. 找到并点击 `Firefox Labs` 实验功能区
4. 找到 `AI 智能助手` 选项并启用

您也可以通过高级配置页面（`about:config`）来启用此功能，具体方法将在下一节介绍。

## 高级配置设置

1. 在 Firefox 地址栏输入 `about:config`
2. 在警告页面点击 `接受风险并继续`
3. 找到 `browser.ml.chat.enabled` 选项并设置为 `true`（如果您还没有通过 Firefox Labs 启用的话）
4. 找到 `browser.ml.chat.hideLocalhost` 选项并设置为 `false`

### 自定义提示词配置

如果您想添加自定义提示词，请按照以下步骤操作：

1. 搜索 `browser.ml.chat.prompts.#`（将 `#` 替换为数字，如 `0`、`1`、`2` 等）
2. 点击 `+` 号添加新的提示词
3. 按照以下格式输入提示词信息：
   ```json
   {
     "id": "我的提示词",
     "value": "这是一个自定义提示词示例。",
     "label": "我的提示词"
   }
   ```
4. 您可以重复上述步骤添加更多提示词

### 配置服务提供者

1. 搜索 `browser.ml.chat.provider`
2. 输入您的 Open WebUI 服务地址，可以包含额外参数，例如：
   ```
   https://your-openwebui.com/?model=assistant&temporary-chat=true&tools=web_search
   ```

## URL 参数配置说明

您可以通过以下 URL 参数来自定义 Open WebUI 的行为：

### AI 模型设置

* `models`：同时启用多个 AI 模型（用逗号分隔），例如：`/?models=gpt4,claude`
* `model`：指定单个默认模型，例如：`/?model=gpt4`

### YouTube 视频转录

* `youtube`：输入 YouTube 视频 ID 来转录视频内容，例如：`/?youtube=VIDEO_ID`

### 网页搜索集成

* `web-search`：启用网页搜索功能，设置为 `true`，例如：`/?web-search=true`

### 工具选择

* `tools` 或 `tool-ids`：启用特定工具（用逗号分隔），例如：`/?tools=search,translate`

### 视频通话

* `call`：启用视频通话界面，设置为 `true`，例如：`/?call=true`

### 默认提示词

* `q`：设置打开时的默认提示词，例如：`/?q=你好，请帮我分析这个网页`

### 临时会话

* `temporary-chat`：创建临时会话，设置为 `true`，例如：`/?temporary-chat=true`

更多 URL 参数使用说明，请访问：https://docs.openwebui.com/features/chat-features/url-params

## 扩展配置选项

在 `about:config` 中还有以下可调整的高级选项：

* `browser.ml.chat.shortcuts`：自定义快捷方式开关
* `browser.ml.chat.shortcuts.custom`：自定义快捷键设置
* `browser.ml.chat.shortcuts.longPress`：长按触发时间
* `browser.ml.chat.sidebar`：侧边栏功能开关
* `browser.ml.checkForMemory`：内存检查功能
* `browser.ml.defaultModelMemoryUsage`：默认内存限制
* `browser.ml.enable`：AI 功能总开关
* `browser.ml.logLevel`：日志级别设置
* `browser.ml.maximumMemoryPressure`：最大内存压力
* `browser.ml.minimumPhysicalMemory`：最小内存要求
* `browser.ml.modelCacheMaxSize`：模型缓存上限
* `browser.ml.modelCacheTimeout`：缓存超时时间
* `browser.ml.modelHubRootUrl`：模型中心地址
* `browser.ml.modelHubUrlTemplate`：模型地址模板
* `browser.ml.queueWaitInterval`：队列等待间隔
* `browser.ml.queueWaitTimeout`：等待超时设置

## 快速访问方式

您可以通过以下两种方式快速打开 AI 智能助手：

* 使用 `CTRL+B` 打开侧边栏，然后切换到 AI 智能助手标签
* 直接使用 `CTRL+Alt+X` 快捷键打开 AI 智能助手
