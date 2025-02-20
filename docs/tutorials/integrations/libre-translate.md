---
sidebar_position: 25
title: "🔠 LibreTranslate 集成"
---

:::warning
本教程来自社区贡献，未经 OpenWebUI 团队官方支持。它仅用于演示如何根据特定需求自定义 OpenWebUI。如果您想要贡献内容，请参考贡献指南教程。
:::

概述
--------

LibreTranslate 是一个免费开源的机器翻译 API，支持多种语言。它是一个自托管、支持离线运行且易于部署的服务。与其他 API 不同，它不依赖 Google 或 Azure 等专有服务提供商来执行翻译，而是使用开源的 [Argos Translate](https://github.com/argosopentech/argos-translate) 库作为翻译引擎。您可以将 LibreTranslate 与 Open WebUI 集成来使用其机器翻译功能。本文档将为您提供在 Docker 中部署 LibreTranslate 并与 Open WebUI 集成的详细指南。

在 Docker 中部署 LibreTranslate
-----------------------------------

请按照以下步骤在 Docker 中部署 LibreTranslate：

### 步骤 1：创建 Docker Compose 文件

在您选择的目录中创建一个名为 `docker-compose.yml` 的新文件，并添加以下配置：

```yml
services:
  libretranslate:
    container_name: libretranslate
    image: libretranslate/libretranslate:v1.6.0
    restart: unless-stopped
    ports:
      - "5000:5000"
    env_file:
      - stack.env
    volumes:
      - libretranslate_api_keys:/app/db
      - libretranslate_models:/home/libretranslate/.local:rw
    tty: true
    stdin_open: true
    healthcheck:
      test: ['CMD-SHELL', './venv/bin/python scripts/healthcheck.py']
      
volumes:
  libretranslate_models:
  libretranslate_api_keys:
```

### 步骤 2：创建 `stack.env` 文件

在同一目录下创建一个名为 `stack.env` 的新文件，添加以下配置：

```bash
# LibreTranslate
LT_DEBUG="false"
LT_UPDATE_MODELS="true"
LT_SSL="false"
LT_SUGGESTIONS="false"
LT_METRICS="false"
LT_HOST="0.0.0.0"

LT_API_KEYS="false"

LT_THREADS="12"
LT_FRONTEND_TIMEOUT="2000"
```

### 步骤 3：启动服务

执行以下命令来启动 LibreTranslate 服务：

```bash
docker-compose up -d
```

该命令将在后台模式启动 LibreTranslate 服务。

配置 Open WebUI 集成
-------------------------------------------

当 LibreTranslate 服务运行后，您就可以配置 Open WebUI 的集成了。目前提供以下几种社区集成方案：

* [LibreTranslate 过滤器函数](https://openwebui.com/f/iamg30/libretranslate_filter)
* [LibreTranslate 动作函数](https://openwebui.com/f/jthesse/libretranslate_action)
* [多语言 LibreTranslate 动作函数](https://openwebui.com/f/iamg30/multilanguage_libretranslate_action)
* [LibreTranslate 过滤器流水线](https://github.com/open-webui/pipelines/blob/main/examples/filters/libretranslate_filter_pipeline.py)

请根据您的具体需求选择合适的集成方案，并按照相应的说明进行配置。

LibreTranslate 流水线与函数支持的语言：
以下是 LibreTranslate 支持的所有语言列表：
```
阿尔巴尼亚语、阿拉伯语、阿塞拜疆语、孟加拉语、保加利亚语、加泰罗尼亚语、瓦伦西亚语、中文、捷克语、丹麦语、荷兰语、英语、弗拉芒语、世界语、爱沙尼亚语、芬兰语、法语、德语、希腊语、希伯来语、印地语、匈牙利语、印度尼西亚语、爱尔兰语、意大利语、日语、韩语、拉脱维亚语、立陶宛语、马来语、波斯语、波兰语、葡萄牙语、罗马尼亚语、摩尔多瓦语、俄语、斯洛伐克语、斯洛文尼亚语、西班牙语、卡斯蒂利亚语、瑞典语、他加禄语、泰语、土耳其语、乌克兰语、乌尔都语
```

故障排除
--------------

* 确保 LibreTranslate 服务正常运行且可以访问
* 检查 Docker 配置是否正确
* 查看 LibreTranslate 的日志信息是否有错误提示

集成优势
----------------------

将 LibreTranslate 集成到 Open WebUI 可以带来以下优势：

* 提供多语言机器翻译功能
* 增强文本分析和处理能力
* 提升语言相关任务的处理能力

结论
----------

将 LibreTranslate 集成到 Open WebUI 是一个简单直接的过程，可以显著增强您的 Open WebUI 实例功能。按照本文档的步骤指引，您可以轻松完成 LibreTranslate 的 Docker 部署和 Open WebUI 的集成配置。
