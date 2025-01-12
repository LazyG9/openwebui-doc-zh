---
sidebar_position: 2
title: "🗨️ 使用 Docker 安装 Kokoro-FastAPI"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何为您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 将 `Kokoro-FastAPI` 🗣️ 集成到 Open WebUI

## 什么是 `Kokoro-FastAPI`？

[Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 是一个基于 Docker 的 FastAPI 封装器，用于 [Kokoro-82M](https://huggingface.co/hexgrad/Kokoro-82M) 文本转语音模型，实现了 OpenAI API 端点规范。它提供高性能的文本转语音功能，具有令人印象深刻的实时处理速度：

- 通过 HF A100 GPU 实现 100 倍以上实时处理速度
- 通过 4060Ti GPU 实现 35-50 倍以上实时处理速度
- 通过 M3 Pro CPU 实现 5 倍以上实时处理速度

## 主要特性

- 兼容 OpenAI 的语音端点，支持内置语音组合
- NVIDIA GPU 加速或 CPU Onnx 推理
- 支持可变分块的流式传输
- 多种音频格式支持（`.mp3`、`.wav`、`.opus`、`.flac`、`.aac`、`.pcm`）
- 内置 Gradio Web UI 界面便于测试
- 提供音素转换和生成端点（用于语音合成的基本声音单元处理）

## 支持的声音

- af
- af_bella
- af_nicole
- af_sarah
- af_sky
- am_adam
- am_michael
- bf_emma
- bf_isabella
- bm_george
- bm_lewis

## 支持的语言

- en_us（美式英语）
- en_uk（英式英语）

## 系统要求

- 已安装 Docker
- 运行中的 Open WebUI
- GPU 版本要求：支持 CUDA 12.1 的 NVIDIA GPU
- CPU 版本：无特殊要求

## ⚡️ 快速开始

### 您可以选择 GPU 或 CPU 版本

### GPU 版本（需要支持 CUDA 12.1 的 NVIDIA GPU）

```bash
docker run -d -p 8880:8880 -p 7860:7860 remsky/kokoro-fastapi:latest
```

### CPU 版本（ONNX 优化推理）

```bash
docker run -d -p 8880:8880 -p 7860:7860 remsky/kokoro-fastapi:cpu-latest
```

## 设置 Open WebUI 使用 `Kokoro-FastAPI`

要在 Open WebUI 中使用 Kokoro-FastAPI，请按照以下步骤操作：

- 打开管理面板，进入 `Settings`（设置）-> `Audio`（音频）
- 将您的 TTS（文本转语音）设置调整为以下配置：
- - 文本转语音引擎：OpenAI
- API 基础 URL：`http://localhost:8880/v1`
- API 密钥：`not-needed`
- TTS 模型：`kokoro`
- TTS 语音：`af_bella`

:::info
默认 API 密钥是字符串 `not-needed`。如果您不需要额外的安全性，则无需更改该值。
:::

## 构建 Docker 容器

```bash
git clone https://github.com/remsky/Kokoro-FastAPI.git
cd Kokoro-FastAPI
docker compose up --build
```

**就是这样！**

# 请查看 [Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 仓库了解如何构建 docker 容器的说明。（用于更改端口等）
