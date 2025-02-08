---
sidebar_position: 2
title: "🗨️ 使用 Docker 部署 Kokoro-FastAPI"
---

:::warning
本教程来自社区贡献，不受 Open WebUI 团队官方支持。仅供参考，展示如何根据特定需求自定义 Open WebUI。如果您也想贡献内容，请参考贡献指南。
:::

# 将 `Kokoro-FastAPI` 🗣️ 集成到 Open WebUI

## 什么是 `Kokoro-FastAPI`？

[Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 是一个基于 Docker 的 FastAPI 包装器，专门用于封装 [Kokoro-82M](https://huggingface.co/hexgrad/Kokoro-82M) 文本转语音模型，实现了 OpenAI API 规范。它提供高性能的文本转语音功能，具有卓越的生成效率：

- 轻量级本地模型（磁盘占用仅需 300MB 以下，CUDA 驱动等额外需要最多 5GB 存储空间）
- 在 HF A100 上可达到 100 倍以上实时速度
- 在 4060Ti 上可达到 35-50 倍以上实时速度
- 在 M3 Pro CPU 上可达到 5 倍以上实时速度
- 超低延迟（GPU 模式下低于 1 秒），可通过分块参数灵活调整

## 核心功能

- 完全兼容 OpenAI 的语音 endpoint，支持内联语音组合
- 支持 NVIDIA GPU 加速或 CPU Onnx 推理
- 支持可调节分块大小的流式传输
- 支持多种音频格式（`.mp3`、`.wav`、`.opus`、`.flac`、`.aac`、`.pcm`）
- 内置网页界面（访问 localhost:8880/web）或可选用仓库中的 Gradio 容器
- 提供音素转换和生成接口

## 支持的语音

- af
- af_bella
- af_irulan
- af_nicole
- af_sarah
- af_sky
- am_adam
- am_michael
- am_gurney
- bf_emma
- bf_isabella
- bm_george
- bm_lewis

## 支持的语言

- en_us（美式英语）
- en_uk（英式英语）

## 运行环境要求

- 已安装 Docker 环境
- Open WebUI 处于运行状态
- GPU 版本：需配备支持 CUDA 12.3 的 NVIDIA GPU
- CPU 版本：无特殊硬件要求

## ⚡️ 快速部署

### 可选 GPU 或 CPU 版本
### GPU 版本（需要支持 CUDA 12.1 的 NVIDIA GPU）

```bash
docker run -d -p 8880:8880 -p 7860:7860 remsky/kokoro-fastapi-gpu:latest
```

### CPU 版本（优化的 ONNX 推理）

```bash
docker run -d -p 8880:8880 -p 7860:7860 remsky/kokoro-fastapi-cpu:latest
```

## 配置 Open WebUI 接入 `Kokoro-FastAPI`

请按照以下步骤配置 Open WebUI：

- 进入管理面板，找到 `设置` -> `音频`
- 按如下参数配置文本转语音设置：
  - 文本转语音引擎：OpenAI
  - API 基础地址：`http://localhost:8880/v1`
  - API 密钥：`not-needed`
  - 文本转语音模型：`kokoro`
  - 语音选项：`af_bella` # 同时兼容现有 OpenAI 语音映射

:::info
系统默认使用 `not-needed` 作为 API 密钥。如无特殊安全需求，可保持此默认值。
:::

## 构建 Docker 容器

```bash
git clone https://github.com/remsky/Kokoro-FastAPI.git
cd Kokoro-FastAPI
cd docker/cpu # 或选择 docker/gpu
docker compose up --build
```

**大功告成！**

## 如需了解更多 Docker 容器构建相关信息（如端口配置等），请访问 [Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 代码仓库
