---
sidebar_position: 2
title: "🗨️ 使用 Docker 部署 Openedai-speech"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队官方支持。它仅作为如何根据特定需求自定义 OpenWebUI 的示例。想要贡献？请查看贡献教程。
:::

**在 Open WebUI 中使用 Docker 集成 `openedai-speech`**
==============================================================

**什么是 `openedai-speech`？**
-----------------------------

:::info
[openedai-speech](https://github.com/matatonic/openedai-speech) 是一个兼容 OpenAI 音频/语音 API 的文本转语音服务器。

它提供 `/v1/audio/speech` 接口，并提供免费、私有的文本转语音体验，支持自定义声音克隆功能。该服务与 OpenAI 没有任何关联，也不需要 OpenAI API 密钥。
:::

**要求**
-----------------

* 系统已安装 Docker
* Open WebUI 运行在 Docker 容器中
* 基本了解 Docker 和 Docker Compose

**方案一：使用 Docker Compose**
----------------------------------

**步骤 1：为 `openedai-speech` 服务创建新文件夹**
-----------------------------------------------------------------

创建一个新文件夹，例如 `openedai-speech-service`，用于存储 `docker-compose.yml` 和 `speech.env` 文件。

**步骤 2：从 GitHub 克隆 `openedai-speech` 仓库**
--------------------------------------------------------------

```bash
git clone https://github.com/matatonic/openedai-speech.git
```

这将下载 `openedai-speech` 仓库到本地，其中包含 Docker Compose 文件（`docker-compose.yml`、`docker-compose.min.yml` 和 `docker-compose.rocm.yml`）以及其他必要文件。

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

你可以使用以下任一 Docker Compose 文件：

* [docker-compose.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.yml)：使用 `ghcr.io/matatonic/openedai-speech` 镜像，从 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建。
* [docker-compose.min.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.min.yml)：使用 `ghcr.io/matatonic/openedai-speech-min` 镜像，从 [Dockerfile.min](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile.min) 构建。
  这是一个最小版本，仅包含 Piper 支持，不需要 GPU。
* [docker-compose.rocm.yml](https://github.com/matatonic/openedai-speech/blob/main/docker-compose.rocm.yml)：使用 `ghcr.io/matatonic/openedai-speech-rocm` 镜像，从 [Dockerfile](https://github.com/matatonic/openedai-speech/blob/main/Dockerfile) 构建，支持 ROCm。

**步骤 4：构建所选 Docker 镜像**
-----------------------------------------

在运行 Docker Compose 文件之前，需要构建 Docker 镜像：

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

* **Nvidia GPU（支持 CUDA）**：运行以下命令以在分离模式下启动 `openedai-speech` 服务：

```bash
docker compose up -d
```

* **AMD GPU（支持 ROCm）**：运行以下命令以在分离模式下启动 `openedai-speech` 服务：

```bash
docker compose -f docker-compose.rocm.yml up -d
```

* **ARM64（Apple M-series，Raspberry Pi）**：XTTS 仅支持 CPU，速度会很慢。你可以使用支持 CPU 的 XTTS 的 Nvidia 镜像（慢），或者使用仅 Piper 的镜像（推荐）：

```bash
docker compose -f docker-compose.min.yml up -d
```

* **仅 CPU，无 GPU（仅 Piper）**：对于一个最小化的 docker 镜像，仅包含 Piper 支持（< 1GB vs. 8GB）：

```bash
docker compose -f docker-compose.min.yml up -d
```

这将启动 `openedai-speech` 服务在分离模式下。

**选项 2：使用 Docker Run 命令**
---------------------------------------

你也可以使用以下 Docker run 命令来启动 `openedai-speech` 服务在分离模式下：

* **Nvidia GPU（支持 CUDA）**：运行以下命令以构建和启动 `openedai-speech` 服务：

```bash
docker build -t ghcr.io/matatonic/openedai-speech .
docker run -d --gpus=all -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech
```

* **ROCm（AMD GPU）**：运行以下命令以构建和启动 `openedai-speech` 服务：

> 要启用 ROCm 支持，请取消 `speech.env` 文件中的 `#USE_ROCM=1` 行。

```bash
docker build -f Dockerfile --build-arg USE_ROCM=1 -t ghcr.io/matatonic/openedai-speech-rocm .
docker run -d --privileged --init --name openedai-speech -p 8000:8000 -v voices:/app/voices -v config:/app/config ghcr.io/matatonic/openedai-speech-rocm
```

* **仅 CPU，无 GPU（仅 Piper）**：运行以下命令以构建和启动 `openedai-speech` 服务：

```bash
docker build -f Dockerfile.min -t ghcr.io/matatonic/openedai-speech-min .
docker run -d -p 8000:8000 -v voices:/app/voices -v config:/app/config --name openedai-speech ghcr.io/matatonic/openedai-speech-min
```

**步骤 6：配置 Open WebUI 以使用 `openedai-speech` 进行 TTS**
---------------------------------------------------------

![openedai-tts](https://github.com/silentoplayz/docs/assets/50341825/ea08494f-2ebf-41a2-bb0f-9b48dd3ace79)

打开 Open WebUI 设置并导航到 **Admin Panel > Settings > Audio** 下的 TTS 设置。添加以下配置：

* **API Base URL**：`http://host.docker.internal:8000/v1`
* **API Key**：`sk-111111111`（请注意，这是一个虚拟 API 密钥，因为 `openedai-speech` 不需要 API 密钥。你可以使用任何你想要的值，只要它被填充）

**步骤 7：选择声音**
--------------------------

在同一个音频设置菜单中，在管理面板中，你可以将 `TTS Model` 设置为以下选择之一，这些选择 `openedai-speech` 支持。这些模型的声音针对英语进行了优化。

* `tts-1` 或 `tts-1-hd`：`alloy`、`echo`、`echo-alt`、`fable`、`onyx`、`nova` 和 `shimmer`（`tts-1-hd` 是可配置的；默认使用 OpenAI 样本）

**步骤 8：按 `Save` 应用更改并开始享受自然声音**
--------------------------------------------------------------------------------------------

按 `Save` 按钮应用更改到你的 Open WebUI 设置。刷新页面以使更改完全生效，并享受使用 `openedai-speech` 集成在 Open WebUI 中进行文本到语音的体验。

**模型详情：**
------------------

`openedai-speech` 支持多种文本到语音模型，每种模型都有其自身的优势和要求。以下模型可用：

* **Piper TTS**（非常快，运行在 CPU 上）：使用 `voice_to_speaker.yaml` 配置文件通过 [Piper voices](https://rhasspy.github.io/piper-samples/)。该模型非常适合需要低延迟和高性能的应用程序。Piper TTS 还支持 [多语言](https://github.com/matatonic/openedai-speech#multilingual) 声音。
* **Coqui AI/TTS XTTS v2**（快速，但需要大约 4GB GPU VRAM 和带有 CUDA 的 Nvidia GPU）：此模型使用 Coqui AI 的 XTTS v2 语音克隆技术生成高质量声音。虽然需要更强大的 GPU，但它提供出色的性能和高质量音频。Coqui 还支持 [多语言](https://github.com/matatonic/openedai-speech#multilingual) 声音。
* **Beta Parler-TTS 支持**（实验性，较慢）：此模型使用 Parler-TTS 框架生成声音。虽然目前处于测试阶段，但它允许你描述扬声器声音的基本特征。每次生成时声音会有所不同，但应该与扬声器描述提供的声音相似。有关如何描述声音的灵感，请参阅 [文本描述到语音](https://www.text-description-to-speech.com/)。

**故障排除**
-------------------

如果你在将 `openedai-speech` 与 Open WebUI 集成时遇到任何问题，请按照以下故障排除步骤进行：

* **验证 `openedai-speech` 服务**：确保 `openedai-speech` 服务正在运行，并且你在 `docker-compose.yml` 文件中指定的端口已暴露。
* **检查 host.docker.internal 的访问权限**：验证主机名 `host.docker.internal` 是否可以从 Open WebUI 容器中解析。这是必要的，因为 `openedai-speech` 通过 `localhost` 在你的 PC 上暴露，但 `open-webui` 无法从其容器内部正常访问它。你可以将卷添加到 `docker-compose.yml` 文件中，以将文件从主机挂载到容器，例如，挂载到将由 `openedai-speech` 服务的目录。
* **审查 API 密钥配置**：确保 API 密钥设置为虚拟值或有效未检查，因为 `openedai-speech` 不需要 API 密钥。
* **检查声音配置**：验证你正在尝试用于 TTS 的声音是否存在于你的 `voice_to_speaker.yaml` 文件中，并且相应的文件（例如，声音 XML 文件）是否存在于正确的目录中。
* **验证声音模型路径**：如果你遇到声音模型加载问题，请双击检查你的 `voice_to_speaker.yaml` 文件中的路径是否与实际的声音模型位置匹配。

**附加故障排除提示**
------------------------------------

* 检查 `openedai-speech` 日志中的错误或警告，以确定问题所在。
* 验证 `docker-compose.yml` 文件是否正确配置了你的环境。
* 如果你仍然遇到问题，请尝试重新启动 `openedai-speech` 服务或整个 Docker 环境。
* 如果问题仍然存在，请咨询 `openedai-speech` GitHub 存储库或寻求相关社区论坛的帮助。

**常见问题**
-------

**如何控制生成音频的情绪范围？**

没有直接机制来控制生成音频的情绪输出。某些因素，如大写或语法，可能会影响音频输出，但内部测试结果是混合的。

**声音文件存储在哪里？配置文件呢？**

配置文件存储在 config 卷中。具体来说，默认声音定义在 voice_to_speaker.default.yaml 中。

**其他资源**
------------------------

有关配置 Open WebUI 以使用 `openedai-speech` 的更多信息，包括设置环境变量，请参阅 [Open WebUI 文档](https://docs.openwebui.com/getting-started/advanced-topics/env-configuration#text-to-speech)。

有关 `openedai-speech` 的更多信息，请访问 [GitHub 存储库](https://github.com/matatonic/openedai-speech)。

**如何为 openedai-speech 添加更多声音：**
[Custom-Voices-HowTo](https://github.com/matatonic/openedai-speech?tab=readme-ov-file#custom-voices-howto)

:::note
你可以更改 `docker-compose.yml` 文件中的端口号，但请确保在 Open WebUI Admin Audio 设置中更新 **API Base URL**。
:::
