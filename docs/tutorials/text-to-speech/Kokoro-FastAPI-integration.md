---
sidebar_position: 2
title: "🗨️ 基于 Docker 的 Kokoro-FastAPI 使用指南"
---

:::warning
本教程来自社区贡献，未经 Open WebUI 团队官方支持。这仅作为自定义 Open WebUI 的示例演示。如果您想要贡献内容，请查看贡献指南。
:::

## 什么是 `Kokoro-FastAPI`？

[Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 是一个基于 Docker 的 FastAPI 封装器，专门用于 [Kokoro-82M](https://huggingface.co/hexgrad/Kokoro-82M) 文本转语音模型，实现了 OpenAI API 接口规范。它提供高性能的文本转语音功能，具有出色的生成速度。

## 主要特性

- 兼容 OpenAI 的语音 API 接口，支持内置语音混合
- 支持 NVIDIA GPU 加速或 CPU Onnx 推理
- 支持可调节块大小的流式传输
- 支持多种音频格式（`.mp3`、`.wav`、`.opus`、`.flac`、`.aac`、`.pcm`）
- 提供集成的 Web 界面（访问 localhost:8880/web）或额外的 Gradio 界面
- 提供音素转换和生成 API 接口

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

## 系统要求

- 已安装 Docker 环境
- 已运行 Open WebUI
- GPU 版本要求：NVIDIA GPU，支持 CUDA 12.3
- CPU 版本：无特殊硬件要求

## ⚡️ 快速开始

### 选择 GPU 或 CPU 版本

### GPU 版本（需要支持 CUDA 12.1 的 NVIDIA GPU）

使用 docker run 命令：

```bash
docker run --gpus all -p 8880:8880 ghcr.io/remsky/kokoro-fastapi-gpu
```

或创建 `docker-compose.yml` 文件并使用 docker compose，示例如下：

```yaml
name: kokoro
services:
    kokoro-fastapi-gpu:
        ports:
            - 8880:8880
        image: ghcr.io/remsky/kokoro-fastapi-gpu:v0.2.1
        restart: always
        deploy:
            resources:
                reservations:
                    devices:
                        - driver: nvidia
                          count: all
                          capabilities:
                              - gpu
```

:::info
使用 GPU 版本前，请确保已安装并配置 [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)
:::

### CPU 版本（优化的 ONNX 推理）

使用 docker run 命令：

```bash
docker run -p 8880:8880 ghcr.io/remsky/kokoro-fastapi-cpu
```

使用 docker compose：

```yaml
name: kokoro
services:
    kokoro-fastapi-cpu:
        ports:
            - 8880:8880
        image: ghcr.io/remsky/kokoro-fastapi-cpu
        restart: always
```

## 配置 Open WebUI 使用 `Kokoro-FastAPI`

按照以下步骤配置 Open WebUI：

- 进入管理面板的 `Settings` -> `Audio` 页面
- 配置 TTS 设置如下：
- - 文本转语音引擎：OpenAI
  - API 基础 URL：`http://localhost:8880/v1`（注意：如果使用 Docker，可能需要将 `localhost` 改为 `host.docker.internal`）
  - API 密钥：`not-needed`
  - TTS 模型：`kokoro`
  - TTS 语音：`af_bella`（支持映射现有的 OpenAI 语音以保持兼容性）

:::info
系统默认使用 `not-needed` 作为 API 密钥。如果您不需要额外的安全措施，可以保持此默认值。
:::

## 构建 Docker 容器

```bash
git clone https://github.com/remsky/Kokoro-FastAPI.git
cd Kokoro-FastAPI
cd docker/cpu # 或选择 docker/gpu
docker compose up --build
```

**配置完成！**

如需了解更多关于 Docker 容器构建的信息（如修改端口等），请访问 [Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) 代码仓库
