---
sidebar_position: 31
title: "🛌 与 Amazon Bedrock 集成"
---

:::warning
本教程是社区贡献内容，不受 Open WebUI 团队官方支持。它仅作为如何根据特定需求自定义 Open WebUI 的示例。想要贡献自己的内容？请查看贡献指南教程。
:::

---

# 将 Open WebUI 与 Amazon Bedrock 集成

本教程将介绍如何将 Open WebUI 与 Amazon Bedrock 进行集成的最常用且实用的方法之一。

## 前提条件

在开始本教程之前，请确保您具备以下条件：

- 一个活跃的 AWS 账户
- 有效的 AWS Access Key 和 Secret Key
- AWS 中用于启用 Bedrock 模型的 IAM 权限或已启用的模型访问权限
- 本地系统已安装 Docker

## 什么是 Amazon Bedrock

以下是来自 AWS 官方网站的介绍：

"Amazon Bedrock 是一个完全托管的服务，通过统一的 API 接口提供来自多家领先 AI 公司的高性能基础模型（FMs），这些公司包括 AI21 Labs、Anthropic、Cohere、Luma、Meta、Mistral AI、poolside（即将推出）、Stability AI 和 Amazon。同时，它还提供构建具有安全性、隐私保护和负责任 AI 特性的生成式 AI 应用所需的全套功能。使用 Amazon Bedrock，您可以轻松地为特定用例测试和评估顶级基础模型，通过微调和检索增强生成（RAG）等技术使用自有数据进行私密定制，并构建能够调用企业系统和数据源执行任务的智能体。由于 Amazon Bedrock 采用无服务器架构，您无需管理基础设施，并且可以使用熟悉的 AWS 服务安全地将生成式 AI 功能集成到您的应用中。"

想要深入了解 Bedrock，请访问：[Amazon Bedrock 官方页面](https://aws.amazon.com/bedrock/)

# 集成步骤

## 步骤 1：验证 Amazon Bedrock 基础模型的访问权限

在开始与 Bedrock 集成之前，您需要首先确认至少能够访问一个（最好是多个）可用的基础模型。截至撰写本文时（2025 年 2 月），平台上共有 47 个基础模型可供使用。如下图所示，您可以看到多个可访问的模型。当模型旁边显示"✅ Access Granted"时，表示您已获得该模型的访问权限。如果您无法访问任何模型，在执行下一步时将会遇到错误。

AWS 提供了详细的模型访问请求和启用文档：[Amazon Bedrock 模型访问文档](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access-modify.html)

![Amazon Bedrock 基础模型](/images/tutorials/amazon-bedrock/amazon-bedrock-base-models.png)

## 步骤 2：配置 Bedrock Access Gateway（BAG）

确认可以访问至少一个 Bedrock 基础模型后，我们需要配置 Bedrock Access Gateway（简称 BAG）。BAG 是由 AWS 开发的一个代理/中间件工具，它封装了 AWS 原生的 Bedrock 端点和 SDK，并提供与 OpenAI 架构兼容的 API 接口，这正是 Open WebUI 所需要的。

下面是端点映射对照表：

| OpenAI 端点 | Bedrock 方法 |
|------------|-------------|
| `/models` | list_inference_profiles |
| `/models/{model_id}` | list_inference_profiles |
| `/chat/completions` | converse 或 converse_stream |
| `/embeddings` | invoke_model |

BAG 项目仓库地址：[Bedrock Access Gateway 仓库](https://github.com/aws-samples/bedrock-access-gateway)

按照以下步骤设置 BAG：
- 克隆 BAG 代码仓库
- 删除默认的 `dockerfile`
- 将 `Dockerfile_ecs` 重命名为 `Dockerfile`

现在我们可以构建并启动 Docker 容器：

```bash
docker build . -f Dockerfile -t bedrock-gateway

docker run -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY="$AWS_SECRET_ACCESS_KEY" -e AWS_REGION=us-east-1 -d -p 8000:80 bedrock-gateway
```

设置完成后，您可以通过访问 http://localhost:8000/docs 查看 BAG 的 Swagger 接口文档。

![Bedrock Access Gateway Swagger](/images/tutorials/amazon-bedrock/amazon-bedrock-proxy-api.png)

## 步骤 3：在 Open WebUI 中添加连接

BAG 服务启动后，我们需要将其作为新的连接添加到 Open WebUI 中。

- 在管理面板中，导航至"设置" -> "连接"
- 点击 OpenAI 部分下的"+"（加号）按钮添加新连接
- 在 URL 字段中输入 "http://host.docker.internal:8000/api/v1"
- 密码填写 BAG 的默认密码 "bedrock"（您可以在 BAG 的 DEFAULT_API_KEYS 设置中修改此密码）
- 点击"验证连接"按钮，如果配置正确，右上角会显示"服务器连接已验证"的提示

![添加新连接](/images/tutorials/amazon-bedrock/amazon-bedrock-proxy-connection.png)

## 步骤 4：开始使用 Bedrock 基础模型

完成以上配置后，您就可以看到并使用所有已授权的 Bedrock 模型了！

![使用 Bedrock 模型](/images/tutorials/amazon-bedrock/amazon-bedrock-models-in-oui.png)

## 其他参考教程

以下是一些在集成 Open WebUI 与 Amazon Bedrock 时非常有帮助的教程资源：

- https://gauravve.medium.com/connecting-open-webui-to-aws-bedrock-a1f0082c8cb2
- https://jrpospos.blog/posts/2024/08/using-amazon-bedrock-with-openwebui-when-working-with-sensitive-data/
