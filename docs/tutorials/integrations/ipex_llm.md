---
sidebar_position: 11
title: "🖥️ 在 Intel GPU 上使用 IPEX-LLM 部署本地大语言模型"
---

:::warning
注意：本教程由社区成员贡献，并非 OpenWebUI 官方团队支持的内容。它仅用于演示如何根据特定需求自定义 OpenWebUI。如果您也想贡献内容，请参考贡献指南教程。
:::

:::note
提示：本指南已通过 Open WebUI 的[手动安装](/getting-started/index.md)方式进行验证。
:::

# 在 Intel GPU 上使用 IPEX-LLM 部署本地大语言模型

:::info
[**IPEX-LLM**](https://github.com/intel-analytics/ipex-llm) 是英特尔开发的 PyTorch 扩展库，专门用于在 Intel CPU 和 GPU 上低延迟运行大语言模型（LLM）。支持的硬件包括：
- 带集成显卡的普通 PC
- Intel Arc A 系列独立显卡
- Intel Flex 系列显卡
- Intel Max 系列显卡
:::

本教程将指导您如何在 Intel GPU 上使用 IPEX-LLM 加速的 Ollama 后端来配置 Open WebUI。通过本指南，即使是配置较低的计算机（如仅有集成显卡的设备），也能流畅运行 Open WebUI。

## 在 Intel GPU 上启动 Ollama 服务

请参考 IPEX-LLM 官方文档中的[快速入门指南](https://ipex-llm.readthedocs.io/en/latest/doc/LLM/Quickstart/ollama_quickstart.html)，了解如何在 Intel GPU 上安装并运行 IPEX-LLM 加速版的 Ollama 服务。

:::tip
重要提示：如果您需要从其他机器访问 Ollama 服务，请确保在执行 `ollama serve` 命令前，设置或导出以下环境变量：
```bash
OLLAMA_HOST=0.0.0.0
```
:::

## 配置 Open WebUI

请按以下步骤配置 Ollama 连接：

1. 在菜单中导航至"设置（Settings）" -> "连接（Connections）"
2. 找到 Ollama 设置部分
3. 默认情况下，"Ollama 基础 URL"设置为 `https://localhost:11434`
4. 点击文本框旁的"刷新"按钮验证连接状态

如果 WebUI 无法连接到 Ollama 服务器，您会看到如下错误提示：
`WebUI could not connect to Ollama`（WebUI 无法连接到 Ollama 服务）

![Ollama 连接失败示例](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings_0.png)

连接成功时，您将看到如下确认消息：
`Service Connection Verified`（服务连接已验证成功）

![Ollama 连接成功示例](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings.png)

:::tip
使用远程 Ollama 服务器？
如果您需要连接到其他地址的 Ollama 服务器：
1. 在"Ollama 基础 URL"中输入新的服务器地址
2. 点击"刷新"按钮重新验证连接
3. 确认看到连接成功的提示
:::
