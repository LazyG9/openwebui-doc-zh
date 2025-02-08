---
sidebar_position: 2
title: "🔧 Pipes"
---

# Pipes
Pipes 是一组在将 LLM 消息返回给用户之前执行特定操作的函数。你可以用 Pipes 执行多种操作，例如检索增强生成（RAG）、向其他 LLM 提供商（如 Anthropic、Azure OpenAI 或 Google）发送请求，或直接在 Web UI 中执行函数。Pipes 可以以函数形式部署，也可以部署在 Pipelines 服务器上。你可以在 [Pipelines 仓库](https://github.com/open-webui/pipelines/tree/main/examples/pipelines)中找到各种示例。下图展示了基本工作流程：

<p align="center">
  <a href="#">
    <img src="/images/pipelines/pipes.png" alt="Pipe Workflow" />
  </a>
</p>

在 WebUI 中定义的 Pipes 会作为新模型出现，并标记为"External"（外部）。下图展示了两个 Pipe 模型（`Database RAG Pipeline` 和 `DOOM`）与两个自托管模型的对比：

<p align="center">
  <a href="#">
    <img src="/images/pipelines/pipe-model-example.png" alt="Pipe Models in WebUI" />
  </a>
</p>
