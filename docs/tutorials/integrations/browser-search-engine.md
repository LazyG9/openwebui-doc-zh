---
sidebar_position: 16
title: "🌐 浏览器搜索引擎集成"
---

:::warning
注意：本教程来自社区贡献，不由 Open WebUI 团队官方支持。它仅用于演示如何根据具体需求自定义 Open WebUI。如果您也想为社区做出贡献，欢迎参考贡献指南。
:::

# 浏览器搜索引擎集成

Open WebUI 支持与您的网络浏览器进行无缝集成。本教程将详细指导您如何将 Open WebUI 设置为浏览器的自定义搜索引擎，让您能够直接在浏览器地址栏中快速执行查询操作。

## 配置 Open WebUI 为搜索引擎

### 环境要求

开始配置之前，请确保满足以下条件：

- 已安装 Chrome 或其他支持的主流浏览器
- 已按照[入门指南](/getting-started/env-configuration)中的说明，通过 Docker 环境变量或 `.env` 文件正确配置了 `WEBUI_URL` 环境变量

### 步骤 1：配置 WEBUI_URL 环境变量

正确设置 `WEBUI_URL` 环境变量是确保浏览器能够准确找到并访问您的 Open WebUI 服务的关键。

#### 使用 Docker 环境变量

如果您使用 Docker 部署 Open WebUI，可以在 `docker run` 命令中设置环境变量：

```bash
docker run -d \
  -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  -e WEBUI_URL="https://<您的open-webui网址>" \
  ghcr.io/open-webui/open-webui:main
```

或者，您也可以在 `.env` 配置文件中添加：

```plaintext
WEBUI_URL=https://<您的open-webui网址>
```

### 步骤 2：添加自定义搜索引擎

### Chrome 浏览器配置

1. 打开 Chrome 浏览器的**设置**页面
2. 在左侧导航栏中选择**搜索引擎**，然后点击**管理搜索引擎和网站搜索**
3. 点击**添加**按钮新建搜索引擎
4. 填写以下配置信息：
    - **搜索引擎名称**：Open WebUI 搜索
    - **快捷关键字**：webui（或其他您习惯的关键词）
    - **搜索网址（%s 表示搜索字词）**：

      ```
      https://<您的open-webui网址>/?q=%s
      ```

5. 点击**添加**完成配置

### Firefox 浏览器配置

方式一：
1. 在 Firefox 中访问您的 Open WebUI 网站
2. 点击地址栏使其展开
3. 在展开的地址栏底部，点击带有绿色圆圈的加号图标，即可将 Open WebUI 添加到浏览器的搜索引擎列表中

方式二：
1. 在 Firefox 中访问您的 Open WebUI 网站
2. 在地址栏中右键点击
3. 从弹出菜单中选择"添加 Open WebUI"（或类似选项）

### 高级配置：指定默认模型

如果您希望搜索时使用特定的 AI 模型，可以在 URL 中指定模型 ID：

```
https://<您的open-webui网址>/?models=<模型ID>&q=%s
```

**注意：** 模型 ID 必须进行 URL 编码处理。例如，如果模型名称包含空格或特殊字符，需要进行编码转换（如 `my model` 需要写成 `my%20model`）。

## 使用方法

完成配置后，您就可以直接在浏览器地址栏中进行搜索。使用方式如下：

```
webui 您要搜索的内容
```

输入后回车，浏览器将自动跳转到 Open WebUI 界面并显示搜索结果。

## 常见问题解决

如果遇到使用问题，请检查以下几点：

- 确认 `WEBUI_URL` 环境变量设置正确，且指向的 Open WebUI 实例可以正常访问
- 检查浏览器搜索引擎设置中的 URL 格式是否完全正确
- 验证网络连接是否正常，Open WebUI 服务是否正常运行
