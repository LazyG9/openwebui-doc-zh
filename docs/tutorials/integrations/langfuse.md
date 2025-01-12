---
sidebar_position: 20
title: "💥 使用 Langfuse 进行监控和调试"
---

# OpenWebUI 的 Langfuse 集成

[Langfuse](https://langfuse.com/)（[GitHub 仓库](https://github.com/langfuse/langfuse)）为 OpenWebUI 提供开源的可观测性和评估功能。通过启用 Langfuse 集成，您可以使用 Langfuse 追踪应用程序数据，从而开发、监控和改进 OpenWebUI 的使用，包括：

- 应用程序[追踪记录](https://langfuse.com/docs/tracing)（实时监控系统行为）
- 使用模式分析
- 按用户和模型统计的成本数据
- 会话重放功能用于问题调试
- [系统评估](https://langfuse.com/docs/scores/overview)

## 如何将 Langfuse 集成到 OpenWebUI

![Langfuse 集成步骤演示](https://langfuse.com/images/docs/openwebui-integration.gif)
_Langfuse 集成步骤说明_

OpenWebUI 中的 [Pipelines](https://github.com/open-webui/pipelines/) 是一个独立于 UI 的 OpenAI API 插件框架。它允许注入能够拦截、处理和转发用户提示到最终大语言模型的插件，从而实现对提示处理的增强控制和自定义。

要使用 Langfuse 追踪应用程序数据，您可以使用 [Langfuse Pipeline](https://github.com/open-webui/pipelines/blob/d4fca4c37c4b8603be7797245e749e9086f35130/examples/filters/langfuse_filter_pipeline.py)，它能够实现消息交互的实时监控和分析。

## 快速入门指南

### 步骤 1：设置 OpenWebUI

首先确保 OpenWebUI 正在运行。详细设置说明请参考 [OpenWebUI 官方文档](https://docs.openwebui.com/)。

### 步骤 2：设置 Pipelines

使用 Docker 启动 [Pipelines](https://github.com/open-webui/pipelines/)。在终端中执行以下命令：

```bash
docker run -p 9099:9099 --add-host=host.docker.internal:host-gateway -v pipelines:/app/pipelines --name pipelines --restart always ghcr.io/open-webui/pipelines:main
```

### 步骤 3：将 OpenWebUI 与 Pipelines 连接

在 OpenWebUI 的"管理设置"页面中，创建并保存一个新的 OpenAI API 类型连接，配置信息如下：

- **URL：** `http://host.docker.internal:9099`（这是之前启动的 Docker 容器的访问地址）
- **密码：** 0p3n-w3bu!（系统默认密码）

![OpenWebUI 设置界面](https://langfuse.com/images/docs/openwebui-setup-settings.png)

### 步骤 4：添加 Langfuse Pipeline

接下来，进入"管理设置" -> "Pipelines"，添加 Langfuse 过滤器管道。设置 Pipelines 监听地址为 `http://host.docker.internal:9099`（与之前配置保持一致），然后使用"从 Github URL 安装"选项安装 [Langfuse 过滤器管道](https://github.com/open-webui/pipelines/blob/main/examples/filters/langfuse_filter_pipeline.py)，安装地址为：

```
https://github.com/open-webui/pipelines/blob/main/examples/filters/langfuse_filter_pipeline.py
```

完成后，在下方配置您的 Langfuse API 密钥。如果您还没有 Langfuse 账户，可以在[Langfuse 官网](https://cloud.langfuse.com)注册账户并获取 API 密钥。

![在 OpenWebUI 中添加 Langfuse Pipeline](https://langfuse.com//images/docs/openwebui-add-pipeline.png)

**重要提示：** 如需在流式传输时捕获 OpenAI 模型的使用情况（Token 计数），请在 OpenWebUI 的模型设置中找到并勾选"功能"部分下的"使用情况"[选项](https://github.com/open-webui/open-webui/discussions/5770#discussioncomment-10778586)。

### 步骤 5：在 Langfuse 中查看追踪记录

完成设置后，您就可以开始使用 OpenWebUI 应用程序，并在 Langfuse 平台中实时查看追踪记录。

查看 Langfuse UI 中的[追踪记录示例](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/904a8c1f-4974-4f8f-8a2f-129ae78d99c5?observation=fe5b127b-e71c-45ab-8ee5-439d4c0edc28)：

![Langfuse 平台中的 OpenWebUI 追踪记录示例](https://langfuse.com/images/docs/openwebui-example-trace.png)

## 了解更多

如需深入了解 OpenWebUI Pipelines 的完整功能和使用方法，请阅读[OpenWebUI Pipelines 入门指南](https://ikasten.io/2024/06/03/getting-started-with-openwebui-pipelines/)。
