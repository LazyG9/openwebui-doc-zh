---
sidebar_position: 2
title: "🔧 Pipes（管道）"
---

# Pipes（管道）
Pipes（管道）是一种特殊的函数，用于在将LLM消息返回给用户之前执行特定操作。你可以通过Pipes实现多种功能，例如：检索增强生成（RAG）、向非OpenAI的LLM服务商（如Anthropic、Azure OpenAI或Google）发送请求，或直接在Web界面中执行函数。Pipes既可以作为Function在本地托管，也可以部署在Pipelines服务器上。您可以在[Pipelines代码仓库](https://github.com/open-webui/pipelines/tree/main/examples/pipelines)中找到更多示例。下图展示了其基本工作流程。

<p align="center">
  <a href="#">
    <img src="/img/pipelines/pipes.png" alt="Pipes工作流程" />
  </a>
</p>

在WebUI中配置的Pipes会作为新的模型出现，并带有"External"（外部）标记。下图展示了两个Pipe模型示例：`Database RAG Pipeline`（数据库RAG管道）和`DOOM`，它们与两个自托管模型一起显示在界面中：

<p align="center">
  <a href="#">
    <img src="/img/pipelines/pipe-model-example.png" alt="WebUI中的Pipe模型示例" />
  </a>
</p>
