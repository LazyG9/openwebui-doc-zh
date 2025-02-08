---
sidebar_position: 2
title: "🐍 Python 代码执行"
---

# 🐍 Python 代码执行

## 概述

Open WebUI 允许在浏览器中执行 Python 代码，利用 Pyodide 在聊天中的代码块内运行脚本。这个功能使大语言模型能够生成可以直接在浏览器中执行的 Python 脚本，并可以使用 Pyodide 支持的各种库。

为了保护用户隐私和提供灵活性，Open WebUI 镜像了 PyPI 包，避免直接的外部网络请求。这种方法也使得 Pyodide 可以在离线环境中使用。

Open WebUI 前端包含一个由 Pyodide 驱动的独立 WebAssembly（WASM）Python 环境，可以执行大语言模型生成的基本 Python 脚本。这个环境设计简单易用，无需额外的设置或安装。

## 支持的库

Pyodide 代码执行环境只会加载在 scripts/prepare-pyodide.js 中配置并添加到 "CodeBlock.svelte" 中的包。Open WebUI 目前支持以下 Pyodide 包：

* micropip
* packaging
* requests
* beautifulsoup4
* numpy
* pandas
* matplotlib
* scikit-learn
* scipy
* regex

这些库可以用于执行各种任务，如数据处理、机器学习和网页抓取。如果您想使用的包未被编译到 Open WebUI 附带的 Pyodide 中，则无法使用该包。

## 执行 Python 代码

要执行 Python 代码，请在聊天中让大语言模型为您编写 Python 脚本。当模型生成代码后，代码块的右上角会出现一个`执行`按钮。点击此按钮将使用 Pyodide 运行代码。要在代码块底部显示结果，请确保代码中至少包含一个 print 语句。

## Python 代码执行使用技巧

* 编写 Python 代码时，请记住代码将在 Pyodide 环境中运行。在请求代码时提到"Pyodide 环境"可以帮助模型生成更适合的代码。
* 建议阅读 Pyodide 文档以了解环境的功能和限制。
* 多尝试不同的库和脚本，探索 Open WebUI 中 Python 代码执行的各种可能性。

## Pyodide 文档

* [Pyodide 文档](https://pyodide.org/en/stable/)

## 代码示例

这是一个可以在 Pyodide 中执行的简单 Python 脚本示例：

```python
import pandas as pd

# 创建样例数据框
data = {'Name': ['John', 'Anna', 'Peter'], 
        'Age': [28, 24, 35]}
df = pd.DataFrame(data)

# 显示数据框
print(df)
```

这个脚本将使用 pandas 创建一个样例数据框，并在聊天中的代码块下方显示它。

## 扩展支持的库

想要扩展可用的功能？要添加更多支持的库，请按照以下步骤操作：

1. **Fork Pyodide 代码仓库**创建您自己的版本。
2. 从 Pyodide 现有的包列表中**选择新的包**，或寻找 Open WebUI 当前缺少的优质包。
3. 将新包**整合**到您的代码仓库中，解锁更多可能性。
