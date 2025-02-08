---
sidebar_position: 1
title: "🗨️ 使用 Docker 的 Edge TTS 集成指南"
---

:::warning
本教程来自社区贡献，并非 Open WebUI 官方团队支持的内容。它仅作为如何根据特定需求自定义 Open WebUI 的示例。如果您也想贡献内容，欢迎查看我们的贡献指南。
:::

# 将 `openai-edge-tts` 🗣️ 集成到 Open WebUI 中

## 什么是 `openai-edge-tts`？

[OpenAI Edge TTS](https://github.com/travisvn/openai-edge-tts) 是一个模仿 OpenAI API 接口的文本转语音服务。在可以自定义接口 URL 的场景中（比如 Open WebUI），它可以直接替代 OpenAI 的语音服务。

该项目使用 [edge-tts](https://github.com/rany2/edge-tts) 包，通过调用 Edge 浏览器的免费"朗读"功能来模拟 Microsoft/Azure 的请求，从而免费提供高质量的文本转语音服务。

[👉 点击这里试听可用的语音样本](https://tts.travisvn.com)

<details>
  <summary>它与 'openedai-speech' 有什么区别？</summary>

与 [openedai-speech](https://github.com/matatonic/openedai-speech) 类似，[openai-edge-tts](https://github.com/travisvn/openai-edge-tts) 都是模仿 OpenAI API 接口的文本转语音服务。它们都可以在支持配置自定义服务器接口 URL 且能调用 OpenAI Speech 接口的场景中使用。

主要区别在于：
- `openedai-speech` 功能更全面，支持完全离线生成语音，并提供多种语音模式选择。
- `openai-edge-tts` 则更加简单轻量，仅通过 `edge-tts` Python 包来生成音频。

</details>

## 系统要求

- 已安装 Docker 环境
- 正在运行的 Open WebUI

## ⚡️ 快速开始

最简单的启动方式是直接运行以下命令，无需任何额外配置：

```bash
docker run -d -p 5050:5050 travisvn/openai-edge-tts:latest
```

这将在 5050 端口启动服务，使用默认配置

## 配置 Open WebUI 使用 `openai-edge-tts`

- 打开管理面板，依次进入 `设置(Settings)` -> `音频(Audio)`
- 按照下方截图配置您的 TTS 设置
- _提示：您可以在此处选择 TTS 语音_

![Open WebUI 管理设置音频配置示例](https://utfs.io/f/MMMHiQ1TQaBobmOhsMkrO6Tl2kxX39dbuFiQ8cAoNzysIt7f)

:::info
默认的 API 密钥是 `your_api_key_here`。如果您不需要额外的安全保护，可以保持此默认值不变。
:::

**配置完成！您现在可以开始使用了**

# 如果您觉得 [OpenAI Edge TTS](https://github.com/travisvn/openai-edge-tts) 对您有帮助，欢迎在 GitHub 上给我们点个 ⭐️ 


<details>
  <summary>使用 Python 运行</summary>
  
### 🐍 使用 Python 运行

如果您更喜欢直接使用 Python 运行此项目，请按照以下步骤设置虚拟环境、安装依赖并启动服务器。

#### 1. 克隆仓库

```bash
git clone https://github.com/travisvn/openai-edge-tts.git
cd openai-edge-tts
```

#### 2. 设置虚拟环境

创建并激活虚拟环境以隔离依赖：

```bash
# macOS/Linux 系统
python3 -m venv venv
source venv/bin/activate

# Windows 系统
python -m venv venv
venv\Scripts\activate
```

#### 3. 安装依赖

使用 `pip` 安装 `requirements.txt` 中列出的必需包：

```bash
pip install -r requirements.txt
```

#### 4. 配置环境变量

在根目录创建 `.env` 文件并设置以下变量：

```plaintext
API_KEY=your_api_key_here
PORT=5050

DEFAULT_VOICE=en-US-AvaNeural
DEFAULT_RESPONSE_FORMAT=mp3
DEFAULT_SPEED=1.0

DEFAULT_LANGUAGE=en-US

REQUIRE_API_KEY=True
REMOVE_FILTER=False
EXPAND_API=True
```

#### 5. 运行服务器

配置完成后，启动服务器：

```bash
python app/server.py
```

服务器将在 `http://localhost:5050` 启动。

#### 6. 测试 API

现在您可以在 `http://localhost:5050/v1/audio/speech` 和其他可用端点上与 API 交互。请参阅[使用说明](#usage)部分了解请求示例。

</details>

<details>
  <summary>使用详情</summary>
  
##### 端点：`/v1/audio/speech`（别名 `/audio/speech`）

从输入文本生成音频。可用参数：

**必需参数：**

- **input**（字符串）：要转换为音频的文本（最多 4096 个字符）。

**可选参数：**

- **model**（字符串）：设置为 "tts-1" 或 "tts-1-hd"（默认：`"tts-1"`）。
- **voice**（字符串）：OpenAI 兼容的语音之一（alloy、echo、fable、onyx、nova、shimmer）或任何有效的 `edge-tts` 语音（默认：`"en-US-AvaNeural"`）。
- **response_format**（字符串）：音频格式。选项：`mp3`、`opus`、`aac`、`flac`、`wav`、`pcm`（默认：`mp3`）。
- **speed**（数字）：播放速度（0.25 到 4.0）。默认为 `1.0`。

:::tip
您可以在 [tts.travisvn.com](https://tts.travisvn.com) 浏览可用的语音并听取样本预览
:::

使用 `curl` 请求并将输出保存为 mp3 文件的示例：

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

或者，按照 OpenAI API 端点参数格式：

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

使用英语以外的语言的示例：

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
- **POST/GET /v1/voices**：列出指定语言/地区的 `edge-tts` 语音。
- **POST/GET /v1/voices/all**：列出所有 `edge-tts` 语音，包含语言支持信息。

:::info
`/v1` 现在是可选的。

此外，还有用于 **Azure AI Speech** 和 **ElevenLabs** 的端点，以便在 Open WebUI 允许这些选项使用自定义 API 端点时提供潜在的未来支持。

可以通过设置环境变量 `EXPAND_API=False` 来禁用这些功能。
:::

</details>

## 🐳 Docker 快速配置

您可以在运行项目的命令中配置环境变量

```bash
docker run -d -p 5050:5050 \
  -e API_KEY=your_api_key_here \
  -e PORT=5050 \
  -e DEFAULT_VOICE=en-US-AvaNeural \
  -e DEFAULT_RESPONSE_FORMAT=mp3 \
  -e DEFAULT_SPEED=1.0 \
  -e DEFAULT_LANGUAGE=en-US \
  -e REQUIRE_API_KEY=True \
  -e REMOVE_FILTER=False \
  -e EXPAND_API=True \
  travisvn/openai-edge-tts:latest
```

:::note
Markdown 文本现在会通过过滤器进行处理，以提高可读性和支持。

您可以通过设置环境变量 `REMOVE_FILTER=True` 来禁用此功能。
:::

## 更多资源

要了解更多关于 `openai-edge-tts` 的信息，您可以访问 [GitHub 仓库](https://github.com/travisvn/openai-edge-tts)

如需直接支持，您可以访问 [Voice AI & TTS Discord](https://tts.travisvn.com/discord)

## 🎙️ 语音样本

[播放语音样本并查看所有可用的 Edge TTS 语音](https://tts.travisvn.com/)
