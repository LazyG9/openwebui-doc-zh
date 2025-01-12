---
sidebar_position: 11
title: "🖥️ 在 Intel GPU 上使用 IPEX-LLM 设置本地 LLM"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

:::note
本指南已通过 [手动安装](/getting-started/index.md) 方式验证 Open WebUI 设置。
:::

# 在 Intel GPU 上使用 IPEX-LLM 设置本地 LLM

:::info
[**IPEX-LLM**](https://github.com/intel-analytics/ipex-llm) 是一个 PyTorch 库，用于在 Intel CPU 和 GPU（例如，带有集成显卡的本地 PC、独立显卡如 Arc A 系列、Flex 和 Max）上以极低的延迟运行 LLM。
:::

本教程演示如何使用 **在 Intel GPU 上运行的 IPEX-LLM 加速 Ollama 后端** 设置 Open WebUI。通过按照本指南操作，您将能够在低成本 PC（即仅有集成显卡）上设置 Open WebUI，并获得流畅的体验。

## 在 Intel GPU 上启动 Ollama 服务

请参考 IPEX-LLM 官方文档中的 [本指南](https://ipex-llm.readthedocs.io/en/latest/doc/LLM/Quickstart/ollama_quickstart.html)，了解如何在 Intel GPU 上安装和运行由 IPEX-LLM 加速的 Ollama 服务。

:::tip
如果您想从另一台机器访问 Ollama 服务，请确保在执行 `ollama serve` 命令之前设置或导出环境变量 `OLLAMA_HOST=0.0.0.0`。
:::

## 配置 Open WebUI

通过菜单中的 **Settings -> Connections** 访问 Ollama 设置。默认情况下，**Ollama Base URL** 预设为 `https://localhost:11434`，如下图所示。要验证 Ollama 服务连接的状态，请单击文本框旁边的 **刷新按钮**。如果 WebUI 无法与 Ollama 服务器建立连接，您将看到一条错误消息，显示 `WebUI could not connect to Ollama`。

![Open WebUI Ollama 设置失败](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings_0.png)

如果连接成功，您将看到一条消息显示 `Service Connection Verified`，如下图所示。

![Open WebUI Ollama 设置成功](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings.png)

:::tip
如果您想使用托管在不同 URL 的 Ollama 服务器，只需将 **Ollama Base URL** 更新为新的 URL，并按 **刷新** 按钮重新确认与 Ollama 的连接。
:::
