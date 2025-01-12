---
sidebar_position: 16
title: "🌐 浏览器搜索引擎"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何为您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 浏览器搜索引擎集成

Open WebUI 允许您直接集成到网络浏览器中。本教程将指导您完成将 Open WebUI 设置为自定义搜索引擎的过程，使您能够从浏览器的地址栏轻松执行查询。

## 将 Open WebUI 设置为搜索引擎

### 前提条件

在开始之前，请确保：

- 您已安装 Chrome 或其他支持的浏览器。
- 已正确设置 `WEBUI_URL` 环境变量，可以使用 Docker 环境变量或在 `.env` 文件中设置，如[入门指南](/getting-started/advanced-topics/env-configuration)中所述。

### 步骤 1：设置 WEBUI_URL 环境变量

设置 `WEBUI_URL` 环境变量可确保您的浏览器知道将查询定向到何处。

#### 使用 Docker 环境变量

如果您使用 Docker 运行 Open WebUI，可以在 `docker run` 命令中设置环境变量：

```bash
docker run -d \
  -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  -e WEBUI_URL="https://<your-open-webui-url>" \
  ghcr.io/open-webui/open-webui:main
```

或者，您可以将变量添加到 `.env` 文件中：

```plaintext
WEBUI_URL=https://<your-open-webui-url>
```

### 步骤 2：将 Open WebUI 添加为自定义搜索引擎

### 对于 Chrome

1. 打开 Chrome 并导航至**设置**。
2. 从侧边栏选择**搜索引擎**，然后点击**管理搜索引擎**。
3. 点击**添加**创建新的搜索引擎。
4. 按如下方式填写详细信息：
    - **搜索引擎**：Open WebUI 搜索
    - **关键字**：webui（或您喜欢的任何关键字）
    - **将 %s 替换为查询的 URL**：

      ```
      https://<your-open-webui-url>/?q=%s
      ```

5. 点击**添加**保存配置。

### 对于 Firefox

1. 在 Firefox 中访问 Open WebUI。
2. 点击地址栏展开它。
3. 点击展开的地址栏底部绿色圆圈中的加号图标。这会将 Open WebUI 的搜索添加到您的首选项中的搜索引擎。

或者：

1. 在 Firefox 中访问 Open WebUI。
2. 右键点击地址栏。
3. 从上下文菜单中选择"添加 Open WebUI"（或类似选项）。

### 可选：使用特定模型

如果您希望使用特定模型进行搜索，请修改 URL 格式以包含模型 ID：

```
https://<your-open-webui-url>/?models=<model_id>&q=%s
```

**注意：** 模型 ID 需要进行 URL 编码。特殊字符（如空格或斜杠）需要编码（例如，`my model` 变为 `my%20model`）。

## 使用示例

设置好搜索引擎后，您可以直接从地址栏执行搜索。只需输入您选择的关键字，后跟您的查询：

```
webui 您的搜索查询
```

此命令将重定向您到 Open WebUI 界面，显示您的搜索结果。

## 故障排除

如果遇到任何问题，请检查以下内容：

- 确保 `WEBUI_URL` 配置正确，并指向有效的 Open WebUI 实例。
- 仔细检查浏览器设置中的搜索引擎 URL 格式是否正确输入。
- 确认您的互联网连接正常，且 Open WebUI 服务运行正常。
