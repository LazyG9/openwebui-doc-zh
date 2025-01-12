---
sidebar_position: 1
title: "🚰 过滤器"
---

# 过滤器

过滤器用于对传入的用户消息和传出的助手（LLM）消息执行操作。过滤器可以执行的潜在操作包括将消息发送到监控平台（如 Langfuse 或 DataDog）、修改消息内容、阻止有害消息、将消息翻译成另一种语言，或限制某些用户的消息频率。[Pipelines 仓库](https://github.com/open-webui/pipelines/tree/main/examples/filters)中维护着一系列示例。过滤器可以作为函数执行或在 Pipelines 服务器上执行。下图展示了一般的工作流程。

<p align="center">
  <a href="#">
    <img src="/img/pipelines/filters.png" alt="过滤器工作流程" />
  </a>
</p>

当在模型或管道上启用过滤器管道时，来自用户的传入消息（或"入口"）会被传递给过滤器进行处理。过滤器在向 LLM 模型请求聊天完成之前对消息执行所需的操作。最后，过滤器在将传出的 LLM 消息（或"出口"）发送给用户之前对其进行后处理。
