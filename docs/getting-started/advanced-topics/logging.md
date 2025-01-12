---
sidebar_position: 5
title: "📜 Open WebUI 日志记录"
---

## 浏览器客户端日志 ##

客户端日志通常通过 [JavaScript](https://developer.mozilla.org/en-US/docs/Web/API/console/log_static) 的 `console.log()` 生成，可以使用浏览器内置的开发者工具访问：

* Blink 引擎
  * [Chrome/Chromium](https://developer.chrome.com/docs/devtools/)
  * [Edge](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/overview)
* Gecko 引擎
  * [Firefox](https://firefox-source-docs.mozilla.org/devtools-user/)
* WebKit 引擎
  * [Safari](https://developer.apple.com/safari/tools/)

## 应用服务器/后端日志 ##

日志系统仍在持续改进中，但可以通过环境变量进行一定程度的控制。[Python 日志](https://docs.python.org/3/howto/logging.html) 的 `log()` 和 `print()` 语句会将信息发送到控制台。默认级别为 `INFO`。出于安全考虑，敏感数据只会在 `DEBUG` 级别下显示。

### 日志级别 ###

支持以下[日志级别](https://docs.python.org/3/howto/logging.html#logging-levels)：

| 级别       | 数值 | 说明 |
| ---------- | --- | ---- |
| `CRITICAL` | 50  | 严重错误 |
| `ERROR`    | 40  | 错误 |
| `WARNING`  | 30  | 警告 |
| `INFO`     | 20  | 信息 |
| `DEBUG`    | 10  | 调试 |
| `NOTSET`   | 0   | 未设置 |

### 全局设置 ###

默认的全局日志级别 `INFO` 可以通过 `GLOBAL_LOG_LEVEL` 环境变量覆盖。设置后，这将在 `config.py` 中执行 `basicConfig` 语句，其中 `force` 参数设置为 `True`。这将导致所有附加的日志记录器重新配置：
> *如果此参数设置为 true，系统会先移除并关闭根日志记录器的所有现有处理程序，然后再应用新的配置。*

数据流使用标准输出（`sys.stdout`）。这不仅影响所有 Open-WebUI 的 `log()` 语句，还会影响使用 Python 日志模块 `basicConfig` 机制的所有导入模块，包括 `urllib`。

例如，要在 Docker 中将日志级别设置为 `DEBUG`，使用：

```
--env GLOBAL_LOG_LEVEL="DEBUG"
```

### 应用/后端 ###

可以使用以下环境变量来实现更精细的日志控制。注意：目前未使用 `basicConfig` 的 `force` 参数，因此这些设置可能只影响 Open-WebUI 日志，而不影响第三方模块。

| 环境变量              | 功能说明                                                         |
| -------------------- | ----------------------------------------------------------------- |
| `AUDIO_LOG_LEVEL`    | 音频转录（faster-whisper、TTS）日志                              |
| `COMFYUI_LOG_LEVEL`  | ComfyUI 集成日志                                                  |
| `CONFIG_LOG_LEVEL`   | 配置相关日志                                                      |
| `DB_LOG_LEVEL`       | Peewee 数据库日志                                                 |
| `IMAGES_LOG_LEVEL`   | AUTOMATIC1111 图像生成日志                                        |
| `MAIN_LOG_LEVEL`     | 主程序执行日志                                                    |
| `MODELS_LOG_LEVEL`   | LLM 模型交互与认证日志                                           |
| `OLLAMA_LOG_LEVEL`   | Ollama 后端交互日志                                              |
| `OPENAI_LOG_LEVEL`   | OpenAI 接口日志                                                   |
| `RAG_LOG_LEVEL`      | RAG（Chroma/Sentence-Transformers）日志                          |
| `WEBHOOK_LOG_LEVEL`  | 认证 webhook 日志                                                 |
