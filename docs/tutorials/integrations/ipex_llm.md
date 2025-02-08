---
sidebar_position: 11
title: "🖥️ 在 Intel GPU 上部署本地大语言模型（IPEX-LLM）"
---

:::warning
注意：本教程来自社区贡献，不由 Open WebUI 团队官方支持。它仅用于演示如何根据具体需求自定义 Open WebUI。如果您也想为社区做出贡献，欢迎参考贡献指南。
:::

:::note
本指南已在[手动安装](/getting-started/index.md) Open WebUI 的环境下进行了验证。
:::

# 在 Intel GPU 上使用 IPEX-LLM 部署本地大语言模型

:::info
[**IPEX-LLM**](https://github.com/intel-analytics/ipex-llm) 是英特尔开发的 PyTorch 扩展库，专门用于在 Intel 处理器和显卡上高效运行大语言模型。它支持多种硬件平台，包括：
- 集成显卡（iGPU）的普通台式机和笔记本
- Arc A 系列等独立显卡
- Flex 系列专业显卡
- Max 系列加速卡
该库的特点是能够实现极低的推理延迟。
:::

本教程将指导您如何使用 **IPEX-LLM 加速的 Ollama 后端**在 Intel GPU 上部署 Open WebUI。即使是只配备了集成显卡的入门级电脑，按照本指南配置后也能流畅运行大语言模型。

## 在 Intel GPU 上部署 Ollama 服务

首先请参考 IPEX-LLM 的[官方快速入门指南](https://ipex-llm.readthedocs.io/en/latest/doc/LLM/Quickstart/ollama_quickstart.html)，了解如何在 Intel GPU 上安装并运行 IPEX-LLM 加速版的 Ollama 服务。

:::tip
远程访问提示：如果您需要从其他设备访问 Ollama 服务，请在运行 `ollama serve` 命令前，设置环境变量：
```bash
export OLLAMA_HOST=0.0.0.0
```
:::

## 配置 Open WebUI 连接

请按照以下步骤配置 Ollama 连接：

1. 打开 Open WebUI，进入**设置 -> 连接**页面
2. 找到 Ollama 设置区域
3. 检查 **Ollama 基础 URL**，默认值应为 https://localhost:11434
4. 点击文本框旁的**刷新按钮**验证连接状态

如果连接失败，您会看到以下错误提示：
![Ollama 连接失败](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings_0.png)

连接成功时，界面显示如下：
![Ollama 连接成功](https://llm-assets.readthedocs.io/en/latest/_images/open_webui_settings.png)

:::tip
远程连接配置：如果您的 Ollama 服务部署在其他服务器上，只需要：
1. 将 **Ollama 基础 URL** 更改为对应的服务器地址
2. 点击**刷新按钮**验证新的连接
:::
