---
sidebar_position: 2
title: "🗨️ 使用 Docker 部署 Openedai-speech"
---

:::warning
本教程是社区贡献内容，不由 OpenWebUI 团队提供支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

**使用 Docker 将 `openedai-speech` 集成到 Open WebUI**
==============================================================

**什么是 `openedai-speech`？**
-----------------------------

:::info
[openedai-speech](https://github.com/matatonic/openedai-speech) 是一个兼容 OpenAI 音频/语音 API 的文字转语音服务器。

它提供 `/v1/audio/speech` 端点，并提供免费、私密的文字转语音体验，具有自定义语音克隆功能。此服务与 OpenAI 没有任何关联，也不需要 OpenAI API 密钥。
:::

**要求**
-----------------

* 系统已安装 Docker
* Open WebUI 在 Docker 容器中运行
* 基本了解 Docker 和 Docker Compose

**选项 1：使用 Docker Compose**
----------------------------------

**步骤 1：为 `openedai-speech` 服务创建新文件夹**
-----------------------------------------------------------------

创建一个新文件夹，例如 `openedai-speech-service`，用于存储 `docker-compose.yml` 和 `speech.env` 文件。

**步骤 2：从 GitHub 克隆 `openedai-speech` 仓库**
--------------------------------------------------------------

```bash
git clone https://github.com/matatonic/openedai-speech.git
```

这将下载 `openedai-speech` 仓库到您的本地机器，其中包括 Docker Compose 文件（`docker-compose.yml`、`docker-compose.min.yml` 和 `docker-compose.rocm.yml`）和其他必要文件。

**步骤 3：将 `sample.env` 文件重命名为 `speech.env`（根据需要自定义）**
------------------------------------------------------------------------------

在 `openedai-speech` 仓库文件夹中，创建一个名为 `speech.env` 的新文件，内容如下：

```yaml
TTS_HOME=voices
HF_HOME=voices
#PRELOAD_MODEL=xtts
#PRELOAD_MODEL=xtts_v2.0.2
#PRELOAD_MODEL=parler-tts/parler_tts_mini_v0.1
#EXTRA_ARGS=--log-level DEBUG --unload-timer 300
#USE_ROCM=1
```

**步骤 4：选择 Docker Compose 文件**
----------------------------------------

您可以使用以下任一 Docker Compose 文件：

* [docker-compose.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.yml)：此文件使用 `ghcr.io/matatonic/openedai-speech` 镜像，从 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建。
* [docker-compose.min.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.min.yml)：此文件使用 `ghcr.io/matatonic/openedai-speech-min` 镜像，从 [Dockerfile.min](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile.min) 构建。
  这是一个最小版本的镜像，仅包含 Piper 支持，不需要 GPU。
* [docker-compose.rocm.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.rocm.yml)：此文件使用 `ghcr.io/matatonic/openedai-speech-rocm` 镜像，从 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建，支持 ROCm。

**步骤 4：构建所选的 Docker 镜像**
-----------------------------------------

在运行 Docker Compose 文件之前，您需要构建 Docker 镜像：

* **Nvidia GPU（支持 CUDA）**：

```bash
docker build -t ghcr.io/matatonic/openedai-speech .
```

* **AMD GPU（支持 ROCm）**：

```bash
docker build -f Dockerfile --build-arg USE_ROCM=1 -t ghcr.io/matatonic/openedai-speech-rocm .
```

* **仅 CPU，无 GPU（仅 Piper）**：

```bash
docker build -f Dockerfile.min -t ghcr.io/matatonic/openedai-speech-min .
```

**步骤 5：运行正确的 `docker compose up -d` 命令**
----------------------------------------------------------

* **Nvidia GPU（支持 CUDA）**：运行以下命令以后台模式启动 `openedai-speech` 服务：

```bash
docker compose up -d
```

* **AMD GPU（支持 ROCm）**：运行以下命令以后台模式启动 `openedai-speech` 服务：

```bash
docker compose -f docker-compose.rocm.yml up -d
```

* **ARM64（Apple M 系列、树莓派）**：XTTS 在这里只支持 CPU，会非常慢。您可以使用 Nvidia 镜像进行 XTTS CPU 运算（较慢），或使用仅 Piper 镜像（推荐）：

```bash
docker compose -f docker-compose.min.yml up -d
```

* **仅 CPU，无 GPU（仅 Piper）**：对于仅支持 Piper 的最小 docker 镜像（< 1GB vs. 8GB）：

```bash
docker compose -f docker-compose.min.yml up -d
```

这将以后台模式启动 `openedai-speech` 服务。

**选项 2：使用 Docker Run 命令**
---------------------------------------

您也可以使用以下 Docker 运行命令以后台模式启动 `openedai-speech` 服务：

* **Nvidia GPU（支持 CUDA）**：运行以下命令以后台模式启动 `openedai-speech` 服务：

```bash
docker build -t ghcr.io/matatonic/openedai-speech .
docker run -d --gpus=all -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech
```

* **ROCm（AMD GPU）**：运行以下命令以后台模式启动 `openedai-speech` 服务：

> 要启用 ROCm 支持，请取消 `speech.env` 文件中的 `#USE_ROCM=1` 行。

```bash
docker build -f Dockerfile --build-arg USE_ROCM=1 -t ghcr.io/matatonic/openedai-speech-rocm .
docker run -d --privileged --init --name openedai-speech -p 8000:8000 -v voices:/app/voices -v config:/app/config ghcr.io/matatonic/openedai-speech-rocm
```

* **仅 CPU，无 GPU（仅 Piper）**：运行以下命令以后台模式启动 `openedai-speech` 服务：

```bash
docker build -f Dockerfile.min -t ghcr.io/matatonic/openedai-speech-min .
docker run -d -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech-min
```

**步骤 6：配置 Open WebUI 以使用 `openedai-speech` 进行 TTS**
---------------------------------------------------------

![openedai-tts](https://github.com/silentoplayz/docs/assets/50341825/ea08494f-2ebf-41a2-bb0f-9b48dd3ace79)

打开 Open WebUI 设置并导航到 **Admin Panel > Settings > Audio** 下的 TTS 设置。添加以下配置：

* **API Base URL**：`http://host.docker.internal:8000/v1`
* **API Key**：`sk-111111111`（请注意，这是一个虚拟 API 密钥，因为 `openedai-speech` 不需要 API 密钥。您可以使用任何您喜欢的值，只要它被填充。）

**步骤 7：选择声音**
--------------------------

在管理面板的同一音频设置菜单中，您可以将 `TTS Model` 设置为以下选项之一，这些选项 `openedai-speech` 支持。这些模型的声音针对英语进行了优化。

* `tts-1` 或 `tts-1-hd`：`alloy`、`echo`、`echo-alt`、`fable`、`onyx`、`nova` 和 `shimmer`（`tts-1-hd` 是可配置的；默认使用 OpenAI 样本）

**步骤 8：按 `Save` 应用更改并开始享受自然声音**
--------------------------------------------------------------------------------------------

按 `Save` 按钮应用更改到您的 Open WebUI 设置。刷新页面以确保更改完全生效，并享受使用 `openedai-speech` 集成在 Open WebUI 中阅读文本响应的文本到语音体验，这些响应以自然声音读出。

**模型详情：**
------------------

`openedai-speech` 支持多种文本到语音模型，每个模型都有其自身的优势和要求。以下模型可用：

* **Piper TTS**（非常快，运行在 CPU 上）：使用 `voice_to_speaker.yaml` 配置文件通过 [Piper voices](https://rhasspy.github.io/piper-samples/)。此模型非常适合需要低延迟和高性能的应用程序。Piper TTS 还支持 [多语言](https://github.com/matatonic/openedai-speech#multilingual) 声音。
* **Coqui AI/TTS XTTS v2**（快速，但需要大约 4GB GPU VRAM 和带有 CUDA 的 Nvidia GPU）：此模型使用 Coqui AI 的 XTTS v2 语音克隆技术生成高质量声音。虽然需要更强大的 GPU，但它提供了出色的性能和高质量音频。Coqui 还支持 [多语言](https://github.com/matatonic/openedai-speech#multilingual) 声音。
* **Beta Parler-TTS 支持**（实验性，较慢）：此模型使用 Parler-TTS 框架生成声音。虽然目前处于测试阶段，但它允许您描述扬声器声音的基本功能。每次生成时声音会略有不同，但应与提供给扬声器的描述相似。要了解如何描述声音，请参阅 [Text Description to Speech](https://www.text-description-to-speech.com/)。

**故障排除**
-------------------

如果您在将 `openedai-speech` 与 Open WebUI 集成时遇到任何问题，请按照以下故障排除步骤进行：

* **验证 `openedai-speech` 服务**：确保 `openedai-speech` 服务正在运行，并且您在 docker-compose.yml 文件中指定的端口已暴露。
* **检查 host.docker.internal 的访问权限**：验证主机名 `host.docker.internal` 是否可以从 Open WebUI 容器中解析。这是必要的，因为 `openedai-speech` 通过 `localhost` 在您的 PC 上暴露，但 `open-webui` 无法从其容器内部正常访问它。您可以在 docker-compose.yml 文件中添加卷，将文件从主机挂载到容器，例如，挂载到将由 `openedai-speech` 服务的目录。
* **审查 API 密钥配置**：确保 API 密钥设置为虚拟值或有效未检查，因为 `openedai-speech` 不需要 API 密钥。
* **检查声音配置**：验证您尝试用于 TTS 的声音是否存在于 `voice_to_speaker.yaml` 文件中，并且相应的文件（例如，声音 XML 文件）是否存在。
* **验证声音模型路径**：如果您遇到声音模型加载问题，请双击检查 `voice_to_speaker.yaml` 文件中的路径是否与实际位置匹配。

**其他故障排除提示**
------------------------------------

* 检查 `openedai-speech` 日志以获取错误或警告，这些错误或警告可能表明问题所在。
* 验证 `docker-compose.yml` 文件是否正确配置了您的环境。
* 如果您仍然遇到问题，请尝试重新启动 `openedai-speech` 服务或整个 Docker 环境。
* 如果问题仍然存在，请咨询 `openedai-speech` GitHub 存储库或寻求相关社区论坛的帮助。

**常见问题**
-------

**如何控制生成音频的情绪范围？**

没有直接机制来控制生成音频的情绪输出。某些因素，例如大写或语法，可能会影响输出音频，但内部测试结果混合。

**声音文件存储在哪里？配置文件呢？**

配置文件存储在 config 卷中。具体来说，默认声音定义在 voice_to_speaker.default.yaml 中。

**其他资源**
------------------------

有关配置 Open WebUI 以使用 `openedai-speech` 的更多信息，包括设置环境变量，请参阅 [Open WebUI 文档](https://docs.openwebui.com/getting-started/advanced-topics/env-configuration#text-to-speech)。

有关 `openedai-speech` 的更多信息，请访问 [GitHub 存储库](https://github.com/matatonic/openedai-speech)。

**如何为 openedai-speech 添加更多声音：**
[Custom-Voices-HowTo](https://github.com/matatonic/openedai-speech?tab=readme-ov-file#custom-voices-howto)

:::note
您可以在 `docker-compose.yml` 文件中更改端口号，但请确保在 Open WebUI Admin Audio 设置中更新 **API Base URL**。
:::
