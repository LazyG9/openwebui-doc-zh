---
sidebar_position: 10
title: "✂️ 减少内存使用"
---

# 减少内存使用

如果您在内存受限的环境中部署此镜像，有几种方法可以减少镜像的内存占用。

在运行 v0.3.10 版本的 Raspberry Pi 4 (arm64) 上，通过以下方法可以将空闲内存消耗从超过 1GB 降低到约 200MB（通过 `docker container stats` 观察到）。

## 简要说明

设置以下环境变量（或对于现有部署设置相应的 UI 选项）：`RAG_EMBEDDING_ENGINE: ollama`，`AUDIO_STT_ENGINE: openai`。

## 详细说明

大部分内存消耗来自于加载的机器学习模型。即使您使用外部语言模型（OpenAI 或独立的 Ollama），许多模型可能会被加载用于其他目的。

截至 v0.3.10 版本，这包括：

* 语音转文本（默认使用 Whisper）
* RAG 嵌入引擎（默认使用本地 SentenceTransformers 模型）
* 图像生成引擎（默认禁用）

前两个功能默认启用并设置为本地模型。您可以在管理面板中更改模型（RAG：在文档类别中，将其设置为 Ollama 或 OpenAI；语音转文本：在音频部分，使用 OpenAI 或 WebAPI）。
如果您正在部署新的 Docker 镜像，也可以通过设置以下环境变量来配置：`RAG_EMBEDDING_ENGINE: ollama`，`AUDIO_STT_ENGINE: openai`。请注意，如果已存在 `config.json`，这些环境变量将不会生效。
