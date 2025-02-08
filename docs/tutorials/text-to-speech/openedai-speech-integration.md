---
sidebar_position: 2
title: "🗨️ 使用 Docker 部署 Openedai-speech"
---

:::warning
本教程来自社区贡献，不受 Open WebUI 团队官方支持。仅作为如何根据特定需求自定义 Open WebUI 的示例。如果您想贡献内容，请查看贡献指南。
:::

**将 `openedai-speech` 集成到 Open WebUI 中（使用 Docker）**
==============================================================

**什么是 `openedai-speech`？**
-----------------------------

:::info
[openedai-speech](https://github.com/matatonic/openedai-speech) 是一个兼容 OpenAI 音频/语音 API 的文本转语音服务器。

它提供 `/v1/audio/speech` API 端点，为用户提供免费、私密的文本转语音体验，并支持自定义语音克隆功能。该服务与 OpenAI 完全无关，无需 OpenAI API 密钥即可使用。
:::

**系统要求**
-----------------

* 系统已安装 Docker
* Open WebUI 以 Docker 容器方式运行
* 具备 Docker 和 Docker Compose 的基本使用知识

**方案一：使用 Docker Compose**
----------------------------------

**步骤 1：创建 `openedai-speech` 服务目录**
-----------------------------------------------------------------

创建一个新目录，比如 `openedai-speech-service`，用于存放 `docker-compose.yml` 和 `speech.env` 配置文件。

**步骤 2：克隆 `openedai-speech` 代码仓库**
--------------------------------------------------------------

```bash
git clone https://github.com/matatonic/openedai-speech.git
```

这将把 `openedai-speech` 仓库下载到本地，其中包含 Docker Compose 配置文件（`docker-compose.yml`、`docker-compose.min.yml` 和 `docker-compose.rocm.yml`）以及其他必需文件。

**步骤 3：配置环境文件**
------------------------------------------------------------------------------

在 `openedai-speech` 目录中，创建 `speech.env` 文件（可从 `sample.env` 复制），内容如下：

```yaml
TTS_HOME=voices
HF_HOME=voices
#PRELOAD_MODEL=xtts
#PRELOAD_MODEL=xtts_v2.0.2
#PRELOAD_MODEL=parler-tts/parler_tts_mini_v0.1
#EXTRA_ARGS=--log-level DEBUG --unload-timer 300
#USE_ROCM=1
```

**步骤 4：选择合适的 Docker Compose 配置**
----------------------------------------

您可以根据需求选择以下配置文件之一：

* [docker-compose.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.yml)：使用 `ghcr.io/matatonic/openedai-speech` 镜像，基于标准 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建。
* [docker-compose.min.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.min.yml)：使用 `ghcr.io/matatonic/openedai-speech-min` 镜像，基于精简版 [Dockerfile.min](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile.min) 构建。
  这是一个轻量级版本，仅包含 Piper 支持，无需 GPU 即可运行。
* [docker-compose.rocm.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.rocm.yml)：使用 `ghcr.io/matatonic/openedai-speech-rocm` 镜像，基于支持 ROCm 的 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建。

**步骤 5：构建 Docker 镜像**
-----------------------------------------

在运行服务之前，需要先构建相应的 Docker 镜像：

* **Nvidia GPU 版本（支持 CUDA）**：

```bash
docker build -t ghcr.io/matatonic/openedai-speech .
```

* **AMD GPU 版本（支持 ROCm）**：

```bash
docker build -f Dockerfile --build-arg USE_ROCM=1 -t ghcr.io/matatonic/openedai-speech-rocm .
```

* **纯 CPU 版本（仅 Piper）**：

```bash
docker build -f Dockerfile.min -t ghcr.io/matatonic/openedai-speech-min .
```

**步骤 6：启动服务**
----------------------------------------------------------

* **Nvidia GPU 版本（CUDA）**：以守护进程方式启动服务：

```bash
docker compose up -d
```

* **AMD GPU 版本（ROCm）**：以守护进程方式启动服务：

```bash
docker compose -f docker-compose.rocm.yml up -d
```

* **ARM64 设备（如 Apple M 系列芯片、树莓派）**：
  注意：在这些设备上 XTTS 只能使用 CPU 运行，速度会很慢。您可以：
  - 选择 Nvidia 镜像搭配 CPU 运行 XTTS（较慢）
  - 使用仅包含 Piper 的轻量级镜像（推荐）：

```bash
docker compose -f docker-compose.min.yml up -d
```

* **纯 CPU 环境（仅 Piper）**：使用轻量级镜像（约 1GB，相比完整版的 8GB 大幅减小）：

```bash
docker compose -f docker-compose.min.yml up -d
```

**方案二：使用 Docker Run 命令**
---------------------------------------

如果您更习惯使用 Docker 命令行，也可以通过以下方式启动服务：

* **Nvidia GPU 版本（CUDA）**：

```bash
docker build -t ghcr.io/matatonic/openedai-speech .
docker run -d --gpus=all -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech
```

* **AMD GPU 版本（ROCm）**：

> 注意：使用 ROCm 前，请先在 `speech.env` 中取消注释 `#USE_ROCM=1` 行。

```bash
docker build -f Dockerfile --build-arg USE_ROCM=1 -t ghcr.io/matatonic/openedai-speech-rocm .
docker run -d --privileged --init --name openedai-speech -p 8000:8000 -v voices:/app/voices -v config:/app/config ghcr.io/matatonic/openedai-speech-rocm
```

* **纯 CPU 版本（仅 Piper）**：

```bash
docker build -f Dockerfile.min -t ghcr.io/matatonic/openedai-speech-min .
docker run -d -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech-min
```

**步骤 7：配置 Open WebUI**
---------------------------------------------------------

![openedai-tts](https://github.com/silentoplayz/docs/assets/50341825/ea08494f-2ebf-41a2-bb0f-9b48dd3ace79)

打开 Open WebUI 设置页面，进入 **管理面板 > 设置 > 音频** 配置以下参数：

* **API 基础 URL**：`http://host.docker.internal:8000/v1`
* **API 密钥**：`sk-111111111`（这是一个示例值，因为 `openedai-speech` 不校验 API 密钥，您可以填写任意内容）

**步骤 8：选择语音模型**
--------------------------

在音频设置中的 `文本转语音语音` 选项下，您可以选择以下 `openedai-speech` 支持的语音模型。这些模型主要针对英语优化：

* `tts-1` 或 `tts-1-hd`：支持 `alloy`、`echo`、`echo-alt`、`fable`、`onyx`、`nova` 和 `shimmer` 等语音（`tts-1-hd` 可自定义配置，默认使用 OpenAI 语音样本）

**步骤 9：保存并开始使用**
--------------------------------------------------------------------------------------------

点击 `保存` 按钮应用设置，然后刷新页面使更改生效。现在您就可以享受 `openedai-speech` 提供的自然语音服务了。

**模型说明**
------------------

`openedai-speech` 支持多种文本转语音模型，每种都有其特点：

* **Piper TTS**（超快速，CPU 运行）：
  - 通过 `voice_to_speaker.yaml` 配置使用 [Piper 语音库](https://rhasspy.github.io/piper-samples/)
  - 适合要求低延迟、高性能的场景
  - 支持[多语言](https://github.com/matatonic/openedai-speech#multilingual)语音

* **Coqui AI/TTS XTTS v2**（高性能，需要 GPU）：
  - 需要约 4GB GPU 显存和支持 CUDA 的 Nvidia GPU
  - 使用 Coqui AI 的 XTTS v2 语音克隆技术
  - 提供高质量音频输出
  - 支持[多语言](https://github.com/matatonic/openedai-speech#multilingual)语音

* **Beta Parler-TTS**（实验性功能）：
  - 使用 Parler-TTS 框架
  - 支持通过文本描述定制语音特征
  - 每次生成的语音略有变化，但会符合描述特征
  - 可参考 [Text Description to Speech](https://www.text-description-to-speech.com/) 了解语音描述方法

**问题排查指南**
-------------------

如果遇到集成问题，请按以下步骤检查：

* **服务状态检查**：
  - 确认 `openedai-speech` 服务正在运行
  - 验证配置的端口已正确暴露

* **网络连接检查**：
  - 确保 Open WebUI 容器能够解析 `host.docker.internal`
  - 必要时在 `docker-compose.yml` 中添加额外的网络配置

* **配置检查**：
  - 确认 API 密钥已设置（可以是任意值）
  - 验证语音配置文件 `voice_to_speaker.yaml` 中的设置
  - 检查语音模型文件路径是否正确

**其他排查建议**
------------------------------------

* 查看 openedai-speech 的日志输出，寻找错误信息
* 检查 `docker-compose.yml` 配置是否符合您的环境
* 尝试重启服务或整个 Docker 环境
* 在 [GitHub 仓库](https://github.com/matatonic/openedai-speech) 或社区论坛寻求帮助

**常见问题解答**
-------

**Q：如何调整语音的情感表现？**
A：目前没有直接的情感控制机制。虽然大小写和标点符号可能会影响语音表现，但效果不一定稳定。

**Q：语音和配置文件存放在哪里？**
A：所有配置文件（包括可用语音定义）都存储在 Docker 的 config 卷中，默认语音配置位于 voice_to_speaker.default.yaml 文件中。

**扩展资源**
------------------------

* **配置指南**：
  - 完整的配置说明请参考 [Open WebUI 文档](https://docs.openwebui.com/getting-started/env-configuration#text-to-speech)
  - 包括环境变量设置、高级配置选项等

* **项目文档**：
  - 详细文档请访问 [openedai-speech GitHub 仓库](https://github.com/matatonic/openedai-speech)
  - 包含最新更新、问题反馈等信息

* **语音定制**：
  - 添加自定义语音请参考[自定义语音使用指南](https://github.com/matatonic/openedai-speech?tab=readme-ov-file#custom-voices-howto)
  - 提供详细的语音添加和配置步骤

:::note
如需更改服务端口，可以修改 `docker-compose.yml` 中的端口映射，但请记得同步更新 Open WebUI 管理面板中的 **API 基础 URL** 设置。
:::
