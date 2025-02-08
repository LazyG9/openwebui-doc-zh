---
sidebar_position: 1
title: "🚰 Filters"
---

# Filters

Filters 用于处理用户的输入消息和 LLM 的输出消息。在 Filters 中可以执行多种操作，例如将消息发送到监控平台（如 Langfuse 或 DataDog）、修改消息内容、拦截不良信息、将消息翻译为其他语言，或对特定用户实施限流。你可以在 [Pipelines 仓库](https://github.com/open-webui/pipelines/tree/main/examples/filters)中找到各种示例。Filters 可以以函数形式运行，也可以在 Pipelines 服务器上运行。下图展示了基本工作流程：

<p align="center">
  <a href="#">
    <img src="/images/pipelines/filters.png" alt="Filter Workflow" />
  </a>
</p>

当在模型或管道上启用过滤管道（filter pipeline）时，用户的输入消息（inlet，入口消息）会被传递给 Filters 处理。Filters 会在请求 LLM 模型进行对话补全之前对消息进行预处理。最后，Filters 会对 LLM 的输出消息（outlet，出口消息）进行后期处理，然后再发送给用户。
