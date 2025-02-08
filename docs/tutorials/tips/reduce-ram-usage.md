---
sidebar_position: 10
title: "✂️ 减少内存使用"
---

# 减少内存使用

如果您在内存受限的环境中部署此镜像，可以采取一些措施来精简镜像。

在运行 v0.3.10 版本的树莓派 4（arm64）上，通过这些措施可以将空闲内存消耗从超过 1GB 降低到约 200MB（通过 `docker container stats` 观察）。

## 简要说明

设置以下环境变量（或对于现有部署，在 UI 中设置相应选项）：`RAG_EMBEDDING_ENGINE: ollama`，`AUDIO_STT_ENGINE: openai`。

## 详细说明

大部分内存消耗来自于加载的机器学习模型。即使您使用外部语言模型（OpenAI 或独立的 Ollama），也可能会为其他用途加载许多模型。

截至 v0.3.10 版本，这包括：

* 语音转文本（默认使用 whisper）
* RAG 嵌入引擎（默认使用本地 SentenceTransformers 模型）
* 图像生成引擎（默认禁用）

前两个默认启用并设置为本地模型。您可以在管理面板中更改模型（RAG：文档类别，设置为 Ollama 或 OpenAI；语音转文本：音频部分，使用 OpenAI 或 WebAPI）。
如果您正在部署新的 Docker 镜像，也可以通过以下环境变量设置：`RAG_EMBEDDING_ENGINE: ollama`，`AUDIO_STT_ENGINE: openai`。请注意，如果已存在 `config.json`，这些环境变量将不会生效。
