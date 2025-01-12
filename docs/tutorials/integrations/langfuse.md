---
sidebar_position: 20
title: "💥 使用 Langfuse 进行监控和调试"
---

# OpenWebUI 的 Langfuse 集成

[Langfuse](https://langfuse.com/) ([GitHub](https://github.com/langfuse/langfuse)) 为 OpenWebUI 提供开源可观察性和评估功能。通过启用 Langfuse 集成，您可以使用 Langfuse 跟踪您的应用程序数据，以开发、监控和改进 OpenWebUI 的使用，包括：

- 应用程序[跟踪](https://langfuse.com/docs/tracing)
- 使用模式
- 按用户和模型的成本数据
- 重放会话以调试问题
- [评估](https://langfuse.com/docs/scores/overview)

## 如何将 Langfuse 与 OpenWebUI 集成

![Langfuse 集成](https://langfuse.com/images/docs/openwebui-integration.gif)
_Langfuse 集成步骤_

OpenWebUI 中的 [Pipelines](https://github.com/open-webui/pipelines/) 是一个与 UI 无关的 OpenAI API 插件框架。它支持注入可以拦截、处理和转发用户提示到最终 LLM 的插件，从而实现对提示处理的增强控制和自定义。

要使用 Langfuse 跟踪您的应用程序数据，您可以使用 [Langfuse pipeline](https://github.com/open-webui/pipelines/blob/d4fca4c37c4b8603be7797245e749e9086f35130/examples/filters/langfuse_filter_pipeline.py)，它支持消息交互的实时监控和分析。

## 快速入门指南

### 步骤 1：设置 OpenWebUI

确保 OpenWebUI 正在运行。为此，请查看 [OpenWebUI 文档](https://docs.openwebui.com/)。

### 步骤 2：设置 Pipelines

使用 Docker 启动 [Pipelines](https://github.com/open-webui/pipelines/)。使用以下命令启动 Pipelines：

```bash
docker run -p 9099:9099 --add-host=host.docker.internal:host-gateway -v pipelines:/app/pipelines --name pipelines --restart always ghcr.io/open-webui/pipelines:main
```

### 步骤 3：将 OpenWebUI 与 Pipelines 连接

在 _管理设置_ 中，创建并保存一个新的 OpenAI API 类型连接，具有以下详细信息：

- **URL：** `http://host.docker.internal:9099`（这是之前启动的 Docker 容器运行的位置）。
- **密码：** 0p3n-w3bu!（标准密码）

![OpenWebUI 设置](https://langfuse.com/images/docs/openwebui-setup-settings.png)

### 步骤 4：添加 Langfuse Filter Pipeline

接下来，导航到 _管理设置_ -> _Pipelines_ 并添加 Langfuse Filter Pipeline。指定 Pipelines 正在监听 `http://host.docker.internal:9099`（如前所述）并使用 _从 Github URL 安装_ 选项安装 [Langfuse Filter Pipeline](https://github.com/open-webui/pipelines/blob/main/examples/filters/langfuse_filter_pipeline.py)，如下所示：

```
https://github.com/open-webui/pipelines/blob/main/examples/filters/langfuse_filter_pipeline.py
```

现在，添加您的 Langfuse API 密钥。如果您尚未注册 Langfuse，您可以通过创建一个帐户 [这里](https://cloud.langfuse.com) 获取您的 API 密钥。

![OpenWebUI add Langfuse Pipeline](https://langfuse.com//images/docs/openwebui-add-pipeline.png)

_**注意：** 捕获使用（令牌计数），当流式传输启用时，对于 OpenAi 模型，您必须导航到 OpenWebUI 中的模型设置并检查 "Usage" [box](https://github.com/open-webui/open-webui/discussions/5770#discussioncomment-10778586) 下 _Capabilities_._

### 步骤 5：在 Langfuse 中查看您的跟踪

您现在可以与您的 OpenWebUI 应用程序交互并查看 Langfuse 中的跟踪。

[示例跟踪](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/904a8c1f-4974-4f8f-8a2f-129ae78d99c5?observation=fe5b127b-e71c-45ab-8ee5-439d4c0edc28)在 Langfuse UI 中：

![OpenWebUI Example Trace in Langfuse](https://langfuse.com/images/docs/openwebui-example-trace.png)

## 了解更多

有关 OpenWebUI Pipelines 的全面指南，请访问 [此帖子](https://ikasten.io/2024/06/03/getting-started-with-openwebui-pipelines/)。
