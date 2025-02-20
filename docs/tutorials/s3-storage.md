---
sidebar_position: 320
title: "🪣 切换到 S3 存储"
---

:::warning
本教程是社区贡献的，不受 Open WebUI 团队支持。它仅作为如何为您的特定用例自定义 Open WebUI 的演示。想要贡献？请查看贡献教程。
:::

# 🪣 切换到 S3 存储

本指南提供了如何将 Open WebUI 配置中的默认 `local` 存储切换到 Amazon S3 的说明。

## 先决条件

为了遵循本教程，您必须具备以下条件：

- 一个活跃的 AWS 账户
- 一个活跃的 AWS 访问密钥和秘密密钥
- 在 AWS 中创建和放置对象到 S3 的 IAM 权限
- 在您的系统上安装了 Docker

## 什么是 Amazon S3

直接来自 AWS 网站：

"Amazon S3 是一种对象存储服务，提供行业领先的可扩展性、数据可用性、安全性和性能。存储和保护任何数量的数据以用于各种用例，例如数据湖、网站、云原生应用程序、备份、归档、机器学习和分析。Amazon S3 设计为 99.999999999%（11 个 9）的持久性，并为全球数百万客户存储数据。"

要了解有关 S3 的更多信息，请访问：[Amazon S3 官方页面](https://aws.amazon.com/s3/)

# 如何设置

## 1. 所需的环境变量

为了配置此选项，您需要收集以下环境变量：

| **Open-WebUI 环境变量** | **示例值**                           |
|-------------------------------------|---------------------------------------------|
| `S3_ACCESS_KEY_ID`                  | ABC123                                      |
| `S3_SECRET_ACCESS_KEY`              | SuperSecret                                 |
| `S3_ENDPOINT_URL`                   | https://s3.us-east-1.amazonaws.com          |
| `S3_REGION_NAME`                    | us-east-1                                   |
| `S3_BUCKET_NAME`                    | my-awesome-bucket-name                      |

- S3_ACCESS_KEY_ID：这是您 AWS 账户访问密钥的标识符。您可以从 AWS 管理控制台或 AWS CLI 创建访问密钥时获取。
- S3_SECRET_ACCESS_KEY：这是您 AWS 访问密钥对的秘密部分。它在您创建 AWS 访问密钥时提供，应安全存储。
- S3_ENDPOINT_URL：此 URL 指向您的 S3 服务端点，通常可以在 AWS 服务文档或账户设置中找到。
- S3_REGION_NAME：这是您的 S3 存储桶所在的 AWS 区域，例如"us-east-1"。您可以在 AWS 管理控制台的 S3 存储桶详细信息下识别。
- S3_BUCKET_NAME：这是您在 AWS 中创建存储桶时指定的 S3 存储桶的唯一名称。

有关可用 S3 端点 URL 的完整列表，请参见：[Amazon S3 常规端点](https://docs.aws.amazon.com/general/latest/gr/s3.html)

在此处查看所有 `Cloud Storage` 配置选项：[Open-WebUI 云存储配置](https://docs.openwebui.com/getting-started/env-configuration#cloud-storage)

## 2. 运行 Open-WebUI

在我们启动 Open-WebUI 实例之前，我们需要设置一个名为 `STORAGE_PROVIDER` 的最终环境变量。此变量告诉 Open-WebUI 您要使用哪个提供商。默认情况下，`STORAGE_PROVIDER` 为空，这意味着 Open-WebUI 使用本地存储。

| **存储提供商** | **类型** | **描述**                                                                                 | **默认** |
|----------------------|----------|-------------------------------------------------------------------------------------------------|-------------|
| `local`              | str      | 如果提供空字符串（`' '`），则默认为本地存储                                | 是         |
| `s3`                 | str      | 使用 S3 客户端库和 Amazon S3 存储中提到的相关环境变量         | 否          |
| `gcs`                | str      | 使用 GCS 客户端库和 Google Cloud Storage 中提到的相关环境变量     | 否          |

要使用 Amazon S3，我们需要将 `STORAGE_PROVIDER` 设置为 "S3"，以及我们在步骤 1 中收集的所有环境变量（`S3_ACCESS_KEY_ID`、`S3_SECRET_ACCESS_KEY`、`S3_ENDPOINT_URL`、`S3_REGION_NAME`、`S3_BUCKET_NAME`）。

在这里，我还将 `ENV` 设置为 "dev"，这将允许我们查看 Open-WebUI Swagger 文档，以便我们可以进一步测试和确认 S3 存储设置是否按预期工作。

```sh
docker run -d \
  -p 3000:8080 \
  -v open-webui:/app/backend/data \
  -e STORAGE_PROVIDER="s3" \
  -e S3_ACCESS_KEY_ID="ABC123" \
  -e S3_SECRET_ACCESS_KEY="SuperSecret" \
  -e S3_ENDPOINT_URL="https://s3.us-east-1.amazonaws.com" \
  -e S3_REGION_NAME="us-east-1" \
  -e S3_BUCKET_NAME="my-awesome-bucket-name" \
  -e ENV="dev" \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

## 3. 测试设置

现在我们已经运行了 Open-WebUI，让我们上传一个简单的 `Hello, World` 文本文件并测试我们的设置。

![在 Open-WebUI 中上传文件](/images/tutorials/amazon-s3/amazon-s3-upload-file.png)

并确认我们从所选的大语言模型中获得了响应。

![在 Open-WebUI 中获取响应](/images/tutorials/amazon-s3/amazon-s3-oui-response.png)

太好了！看起来一切都在 Open-WebUI 中按预期工作。现在让我们验证文本文件确实已上传并存储在指定的 S3 存储桶中。使用 AWS 管理控制台，我们可以看到 S3 存储桶中现在有一个文件。除了我们上传的文件名（`hello.txt`）之外，您还可以看到对象的名称附加了一个唯一 ID。这就是 Open-WebUI 跟踪所有上传文件的方式。

![在 Open-WebUI 中获取响应](/images/tutorials/amazon-s3/amazon-s3-object-in-bucket.png)

使用 Open-WebUI 的 swagger 文档，我们可以使用 `/api/v1/files/{id}` 端点并传入唯一 ID（4405fabb-603e-4919-972b-2b39d6ad7f5b）获取与此文件相关的所有信息。

![通过 ID 检查文件](/images/tutorials/amazon-s3/amazon-s3-get-file-by-id.png)
