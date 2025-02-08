---
sidebar_position: 20
title: "💥 使用 Langfuse 实现应用监控与调试"
---

# OpenWebUI 集成 Langfuse 监控系统

[Langfuse](https://langfuse.com/)（[GitHub 仓库](https://github.com/langfuse/langfuse)）是一个开源的应用监控和评估平台，专为 OpenWebUI 设计。启用 Langfuse 集成后，您可以实现以下功能：

- 应用程序全链路[数据追踪](https://langfuse.com/docs/tracing)
- 用户行为模式分析
- 按用户和模型维度的成本统计
- 会话回放与问题诊断
- 系统性能[评估](https://langfuse.com/docs/scores/overview)

## Langfuse 集成指南

![Langfuse 集成流程](https://langfuse.com/images/docs/openwebui-integration.gif)
_集成步骤演示_

OpenWebUI 的[管道系统](https://github.com/open-webui/pipelines/)是一个独立于界面的 OpenAI API 插件框架。它允许您注入自定义插件来拦截、处理和转发用户输入，实现对大语言模型交互过程的精确控制和定制化处理。

如果您想追踪应用数据，可以使用 [Langfuse 管道插件](https://github.com/open-webui/pipelines/blob/d4fca4c37c4b8603be7797245e749e9086f35130/examples/filters/langfuse_filter_pipeline.py)，它能够实现对消息交互的实时监控和深度分析。

## 快速部署指南

### 步骤 1：准备环境

首先确保您已经正确安装并运行了 OpenWebUI。如需帮助，请参考 [OpenWebUI 官方文档](https://docs.openwebui.com/)。

### 步骤 2：部署管道服务

使用以下 Docker 命令启动管道服务：

```bash
docker run -p 9099:9099 --add-host=host.docker.internal:host-gateway -v pipelines:/app/pipelines --name pipelines --restart always ghcr.io/open-webui/pipelines:main
```

### 步骤 3：配置 OpenWebUI 连接

进入 OpenWebUI 的**管理设置**，创建一个新的 OpenAI API 连接：

- **服务地址：** `http://host.docker.internal:9099`（指向刚才部署的 Docker 容器）
- **访问密码：** 0p3n-w3bu!（系统默认密码）

![OpenWebUI 连接配置](https://langfuse.com/images/docs/openwebui-setup-settings.png)

### 步骤 4：安装 Langfuse 管道

1. 进入**管理设置** -> **管道配置**
2. 添加 Langfuse 监控管道
3. 确认管道服务地址为http://host.docker.internal:9099
4. 使用以下 URL 通过**Github 链接安装**选项安装管道插件：

```
https://github.com/open-webui/pipelines/blob/main/examples/filters/langfuse_filter_pipeline.py
```

5. 配置您的 Langfuse API 密钥（如果还没有账号，请在[这里注册](https://cloud.langfuse.com)获取）

![Langfuse 管道配置](https://langfuse.com//images/docs/openwebui-add-pipeline.png)

**重要提示：** 如果您需要在流式输出模式下统计 OpenAI 模型的令牌用量，请在 OpenWebUI 的模型设置中找到**功能**部分，勾选"使用统计"[选项](https://github.com/open-webui/open-webui/discussions/5770#discussioncomment-10778586)。

### 步骤 5：查看监控数据

完成配置后，您可以正常使用 OpenWebUI，所有的交互数据都会被记录在 Langfuse 平台中。

以下是 Langfuse 界面中的[数据示例](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/904a8c1f-4974-4f8f-8a2f-129ae78d99c5?observation=fe5b127b-e71c-45ab-8ee5-439d4c0edc28)：

![监控数据示例](https://langfuse.com/images/docs/openwebui-example-trace.png)

## 深入学习

如果您想深入了解 OpenWebUI 管道系统的更多功能，推荐阅读[这篇详细教程](https://ikasten.io/2024/06/03/getting-started-with-openwebui-pipelines/)。
