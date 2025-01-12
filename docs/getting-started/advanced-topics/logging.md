---
sidebar_position: 5
title: "📜 Open WebUI 日志记录"
---

## 浏览器客户端日志记录 ##

客户端日志通常通过 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/API/console/log_static) `console.log()` 生成，可以使用内置的浏览器特定开发者工具访问：

* Blink
  * [Chrome/Chromium](https://developer.chrome.com/docs/devtools/)
  * [Edge](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/overview)
* Gecko
  * [Firefox](https://firefox-source-docs.mozilla.org/devtools-user/)
* WebKit
  * [Safari](https://developer.apple.com/safari/tools/)

## 应用服务器/后端日志记录 ##

日志记录功能仍在持续改进中，但可以通过环境变量进行一定程度的控制。[Python Logging](https://docs.python.org/3/howto/logging.html) `log()` 和 `print()` 语句将信息发送到控制台。默认级别为 `INFO`。理想情况下，敏感数据只会在 `DEBUG` 级别下显示。

### 日志级别 ###

支持以下[日志级别](https://docs.python.org/3/howto/logging.html#logging-levels)值：

| 级别       | 数值          |
| ---------- | ------------- |
| `CRITICAL` | 50            |
| `ERROR`    | 40            |
| `WARNING`  | 30            |
| `INFO`     | 20            |
| `DEBUG`    | 10            |
| `NOTSET`   | 0             |

### 全局设置 ###

默认的全局日志级别 `INFO` 可以通过 `GLOBAL_LOG_LEVEL` 环境变量覆盖。设置后，这将在 `config.py` 中执行 [basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig) 语句，其中 `force` 参数设置为 *True*。这将导致所有附加的日志记录器重新配置：
> *如果此关键字参数指定为 true，则在执行其他参数指定的配置之前，将删除并关闭附加到根日志记录器的所有现有处理程序。*

数据流使用标准输出（`sys.stdout`）。除了所有 Open-WebUI `log()` 语句外，这还会影响使用 Python Logging 模块 `basicConfig` 机制的任何导入的 Python 模块，包括 [urllib](https://docs.python.org/3/library/urllib.html)。

例如，要将 Docker 参数设置为 `DEBUG` 日志级别，请使用：

```
--env GLOBAL_LOG_LEVEL="DEBUG"
```

### 应用/后端 ###

使用以下任意组合的变量可以实现一定程度的精细控制。请注意，目前未使用 `basicConfig` `force`，因此这些语句可能只影响 Open-WebUI 日志记录，而不影响第三方模块。

| 环境变量              | 应用/后端                                                        |
| -------------------- | ----------------------------------------------------------------- |
| `AUDIO_LOG_LEVEL`    | 使用 faster-whisper、TTS 等进行音频转录                          |
| `COMFYUI_LOG_LEVEL`  | ComfyUI 集成处理                                                  |
| `CONFIG_LOG_LEVEL`   | 配置处理                                                          |
| `DB_LOG_LEVEL`       | 内部 Peewee 数据库                                                |
| `IMAGES_LOG_LEVEL`   | AUTOMATIC1111 稳定扩散图像生成                                    |
| `MAIN_LOG_LEVEL`     | 主要（根）执行                                                    |
| `MODELS_LOG_LEVEL`   | LLM 模型交互、认证等                                             |
| `OLLAMA_LOG_LEVEL`   | Ollama 后端交互                                                   |
| `OPENAI_LOG_LEVEL`   | OpenAI 交互                                                       |
| `RAG_LOG_LEVEL`      | 使用 Chroma/Sentence-Transformers 的检索增强生成                  |
| `WEBHOOK_LOG_LEVEL`  | 认证 webhook 扩展日志记录                                         |
