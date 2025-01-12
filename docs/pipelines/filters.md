---
sidebar_position: 1
title: "🚰 过滤器"
---

# 过滤器

过滤器用于处理用户的输入消息和AI助手（LLM）的输出消息。过滤器可以执行多种操作，包括：将消息发送至监控平台（如Langfuse或DataDog）、修改消息内容、拦截有害消息、将消息翻译为其他语言，或对特定用户实施访问频率限制。您可以在[Pipelines代码仓库](https://github.com/open-webui/pipelines/tree/main/examples/filters)中找到更多示例。过滤器既可以作为Function在本地执行，也可以在Pipelines服务器上运行。下图展示了其基本工作流程。

<p align="center">
  <a href="#">
    <img src="/img/pipelines/filters.png" alt="过滤器工作流程" />
  </a>
</p>

当在模型或管道中启用过滤器pipeline时，用户的输入消息（inlet，输入端）首先会传递给过滤器进行处理。过滤器会对消息执行预设的操作，随后向LLM模型请求对话补全。最后，过滤器会对LLM的返回消息（outlet，输出端）进行后处理，再将处理后的消息发送给用户。
