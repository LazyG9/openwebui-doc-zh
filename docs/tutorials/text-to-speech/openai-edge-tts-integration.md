---
sidebar_position: 1
title: "🗨️ 使用 Docker 部署 Edge TTS"
---

:::warning
本教程是社区贡献内容，不由 OpenWebUI 团队提供支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 将 `openai-edge-tts` 🗣️ 集成到 Open WebUI

## 什么是 `openai-edge-tts`，它与 `openedai-speech` 有什么不同？

与 [openedai-speech](https://github.com/matatonic/openedai-speech) 类似，[openai-edge-tts](https://github.com/travisvn/openai-edge-tts) 是一个模仿 OpenAI API 端点的文字转语音 API 端点，可以在需要调用 OpenAI Speech 端点且可以配置服务器端点 URL 的场景中直接替代。

`openedai-speech` 是一个更全面的选项，允许使用多种模式进行完全离线的语音生成。

`openai-edge-tts` 是一个更简单的选项，使用名为 `edge-tts` 的 Python 包来生成音频。

`edge-tts`（[仓库](https://github.com/rany2/edge-tts)）利用 Edge 浏览器的免费"朗读"功能来模拟对 Microsoft / Azure 的请求，以免费获得非常高质量的文字转语音服务。

## 要求

- 系统已安装 Docker
- Open WebUI 正在运行
- ffmpeg（可选 - 仅在不使用 `mp3` 格式时需要）

## ⚡️ 快速开始

最简单的方法是运行以下命令，而无需配置任何内容：

```bash
docker run -d -p 5050:5050 travisvn/openai-edge-tts:latest
```

这将在端口 5050 上运行服务，使用所有默认配置。

## 设置 Open WebUI 使用 `openai-edge-tts`

- 打开管理面板，进入 设置 -> 音频
- 将您的 TTS 设置调整为与下面的截图相匹配
- _注意：您可以在这里指定 TTS 语音_

![为此项目添加正确端点的 Open WebUI 管理设置音频截图](https://utfs.io/f/MMMHiQ1TQaBobmOhsMkrO6Tl2kxX39dbuFiQ8cAoNzysIt7f)

:::info
默认的 API 密钥是字符串 `your_api_key_here`。如果您不需要额外的安全性，则无需更改该值。
:::

**就是这样！您可以在这里结束**

查看 [使用](#usage) 部分获取请求示例。

# 如果您觉得 [OpenAI Edge TTS](https://github.com/travisvn/openai-edge-tts) 有用，请在 GitHub 上给项目点 ⭐️ 星标

:::tip
您可以直接在 `docker run` 命令中定义环境变量。请参阅下面的 [Docker 快速配置](#-quick-config-for-docker)。
:::

## 替代选项

### 🐍 使用 Python 运行

如果您更喜欢直接使用 Python 运行此项目，请按照以下步骤设置虚拟环境、安装依赖项并启动服务器。

#### 1. 克隆仓库

```bash
git clone https://github.com/travisvn/openai-edge-tts.git
cd openai-edge-tts
```

#### 2. 设置虚拟环境

创建并激活虚拟环境以隔离依赖项：

```bash
# 对于 macOS/Linux
python3 -m venv venv
source venv/bin/activate

# 对于 Windows
python -m venv venv
venv\Scripts\activate
```

#### 3. 安装依赖项

使用 `pip` 安装 `requirements.txt` 中列出的必需包：

```bash
pip install -r requirements.txt
```

#### 4. 配置环境变量

在根目录创建 `.env` 文件并设置以下变量：

```plaintext
API_KEY=your_api_key_here
PORT=5050

DEFAULT_VOICE=en-US-AndrewNeural
DEFAULT_RESPONSE_FORMAT=mp3
DEFAULT_SPEED=1.2

DEFAULT_LANGUAGE=en-US

REQUIRE_API_KEY=True
REMOVE_FILTER=False
EXPAND_API=True
```

#### 5. 运行服务器

配置完成后，使用以下命令启动服务器：

```bash
python app/server.py
```

服务器将在 `http://localhost:5050` 上运行。

#### 6. 测试 API

您现在可以在 `http://localhost:5050/v1/audio/speech` 和其他可用端点上与 API 交互。查看 [使用](#usage) 部分获取请求示例。

#### 使用

##### 端点：`/v1/audio/speech`（别名 `/audio/speech`）

从输入文本生成音频。可用参数：

**必需参数：**

- **input**（字符串）：要转换为音频的文本（最多 4096 个字符）。

**可选参数：**

- **model**（字符串）：设置为 "tts-1" 或 "tts-1-hd"（默认：`"tts-1"`）。
- **voice**（字符串）：OpenAI 兼容的语音之一（alloy、echo、fable、onyx、nova、shimmer）或任何有效的 `edge-tts` 语音（默认：`"en-US-AndrewNeural"`）。
- **response_format**（字符串）：音频格式。选项：`mp3`、`opus`、`aac`、`flac`、`wav`、`pcm`（默认：`mp3`）。
- **speed**（数字）：播放速度（0.25 到 4.0）。默认为 `1.2`。

:::tip
您可以在 [tts.travisvn.com](https://tts.travisvn.com) 浏览可用的语音并听取示例预览
:::

使用 `curl` 的请求示例，并将输出保存为 mp3 文件：

```bash
curl -X POST http://localhost:5050/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_api_key_here" \
  -d '{
    "input": "Hello, I am your AI assistant! Just let me know how I can help bring your ideas to life.",
    "voice": "echo",
    "response_format": "mp3",
    "speed": 1.0
  }' \
  --output speech.mp3
```

或者，为了与 OpenAI API 端点参数保持一致：

```bash
curl -X POST http://localhost:5050/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_api_key_here" \
  -d '{
    "model": "tts-1",
    "input": "Hello, I am your AI assistant! Just let me know how I can help bring your ideas to life.",
    "voice": "alloy"
  }' \
  --output speech.mp3
```

以及一个非英语的示例：

```bash
curl -X POST http://localhost:5050/v1/audio/speech \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your_api_key_here" \
  -d '{
    "model": "tts-1",
    "input": "じゃあ、行く。電車の時間、調べておくよ。",
    "voice": "ja-JP-KeitaNeural"
  }' \
  --output speech.mp3
```

##### 其他端点

- **POST/GET /v1/models**：列出可用的 TTS 模型。
- **POST/GET /v1/voices**：列出给定语言/区域设置的 `edge-tts` 语音。
- **POST/GET /v1/voices/all**：列出所有 `edge-tts` 语音，包含语言支持信息。

:::info
`/v1` 现在是可选的。

此外，还有用于 **Azure AI Speech** 和 **ElevenLabs** 的端点，以便在 Open WebUI 允许这些选项使用自定义 API 端点时提供潜在的未来支持。

可以通过设置环境变量 `EXPAND_API=False` 来禁用这些功能。
:::

## 🐳 Docker 快速配置

您可以在运行项目的命令中配置环境变量：

```bash
docker run -d -p 5050:5050 \
  -e API_KEY=your_api_key_here \
  -e PORT=5050 \
  -e DEFAULT_VOICE=en-US-AndrewNeural \
  -e DEFAULT_RESPONSE_FORMAT=mp3 \
  -e DEFAULT_SPEED=1.2 \
  -e DEFAULT_LANGUAGE=en-US \
  -e REQUIRE_API_KEY=True \
  -e REMOVE_FILTER=False \
  -e EXPAND_API=True \
  travisvn/openai-edge-tts:latest
```

:::note
Markdown 文本现在会通过过滤器以增强可读性和支持。

您可以通过设置环境变量 `REMOVE_FILTER=True` 来禁用此功能。
:::

## 其他资源

要了解更多关于 `openai-edge-tts` 的信息，您可以访问 [GitHub 仓库](https://github.com/travisvn/openai-edge-tts)

如需直接支持，您可以访问 [Voice AI & TTS Discord](https://tts.travisvn.com/discord)

## 🎙️ 语音示例

[播放语音示例并查看所有可用的 Edge TTS 语音](https://tts.travisvn.com/)
