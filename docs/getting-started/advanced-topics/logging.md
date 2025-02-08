---
sidebar_position: 5
title: "📜 Open WebUI 日志"
---

## 浏览器客户端日志 ##

客户端日志主要通过 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/API/console/log_static) 的 `console.log()` 生成，可通过浏览器内置的开发者工具查看：

* Blink
  * [Chrome/Chromium](https://developer.chrome.com/docs/devtools/)
  * [Edge](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/overview)
* Gecko
  * [Firefox](https://firefox-source-docs.mozilla.org/devtools-user/)
* WebKit
  * [Safari](https://developer.apple.com/safari/tools/)

## 应用服务器/后端日志 ##

日志系统仍在持续优化中，但可以通过环境变量进行一定程度的控制。[Python 日志](https://docs.python.org/3/howto/logging.html) 的 `log()` 和 `print()` 语句会将信息输出到控制台。默认级别为 `INFO`。出于安全考虑，敏感数据仅在 `DEBUG` 级别下显示。

### 日志级别 ###

支持以下[日志级别](https://docs.python.org/3/howto/logging.html#logging-levels)：

| 级别       | 数值          |
| ---------- | ------------- |
| `CRITICAL` | 50            |
| `ERROR`    | 40            |
| `WARNING`  | 30            |
| `INFO`     | 20            |
| `DEBUG`    | 10            |
| `NOTSET`   | 0             |

### 全局设置 ###

默认的全局日志级别 `INFO` 可通过 `GLOBAL_LOG_LEVEL` 环境变量覆盖。设置后，将在 `config.py` 中执行 [basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig) 语句，其中 `force` 参数设为 *True*。这将触发所有已附加日志记录器的重新配置：
> *如果此关键字参数设为 true，系统会在执行其他参数指定的配置前，移除并关闭所有附加到根日志记录器的现有处理程序。*

数据流使用标准输出（`sys.stdout`）。除了所有 Open-WebUI 的 `log()` 语句外，这还会影响使用 Python 日志模块 `basicConfig` 机制的所有导入模块，包括 [urllib](https://docs.python.org/3/library/urllib.html)。

例如，要在 Docker 参数中将日志级别设置为 `DEBUG`，使用：

```
--env GLOBAL_LOG_LEVEL="DEBUG"
```

### 应用/后端 ###

通过以下环境变量组合可实现细粒度控制。请注意，目前未启用 `basicConfig` 的 `force` 参数，因此这些设置可能仅影响 Open-WebUI 的日志，而不影响第三方模块。

| 环境变量              | 功能说明                                                         |
| -------------------- | ----------------------------------------------------------------- |
| `AUDIO_LOG_LEVEL`    | 使用 faster-whisper、TTS 等的音频转录                            |
| `COMFYUI_LOG_LEVEL`  | ComfyUI 集成处理                                                  |
| `CONFIG_LOG_LEVEL`   | 配置处理                                                          |
| `DB_LOG_LEVEL`       | 内部 Peewee 数据库                                                |
| `IMAGES_LOG_LEVEL`   | AUTOMATIC1111 stable diffusion 图像生成                           |
| `MAIN_LOG_LEVEL`     | 主程序（根）执行                                                  |
| `MODELS_LOG_LEVEL`   | 大语言模型交互、认证等                                           |
| `OLLAMA_LOG_LEVEL`   | Ollama 后端交互                                                   |
| `OPENAI_LOG_LEVEL`   | OpenAI 交互                                                       |
| `RAG_LOG_LEVEL`      | 使用 Chroma/Sentence-Transformers 的检索增强生成（RAG）          |
| `WEBHOOK_LOG_LEVEL`  | Webhook 认证扩展日志                                              |
