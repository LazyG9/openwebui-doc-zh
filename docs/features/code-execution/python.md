---
sidebar_position: 2
title: "🐍 Python 代码执行"
---

# 🐍 Python 代码执行

## 概述

Open WebUI 允许在浏览器中客户端执行 Python 代码，利用 Pyodide 在聊天中的代码块内运行脚本。此功能使大型语言模型（LLM）能够生成可以直接在浏览器中执行的 Python 脚本，利用 Pyodide 支持的各种库。

为了维护用户隐私和灵活性，Open WebUI 镜像了 PyPI 包，避免直接的外部网络请求。这种方法还使得在没有互联网访问的环境中也能使用 Pyodide。

Open WebUI 前端包含一个由 Pyodide 提供支持的独立 WASM（WebAssembly）Python 环境，可以执行 LLM 生成的基本 Python 脚本。这个环境设计为易于使用，无需额外的设置或安装。

## 支持的库

Pyodide 代码执行配置为仅加载在 scripts/prepare-pyodide.js 中配置并添加到 "CodeBlock.svelte" 中的包。以下是 Open WebUI 当前支持的 Pyodide 包：

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

这些库可用于执行各种任务，如数据操作、机器学习和网页抓取。如果您想运行的包没有编译在我们随 Open WebUI 一起提供的 Pyodide 中，则无法使用该包。

## 调用 Python 代码执行

要执行 Python 代码，请在聊天中要求 LLM 为您编写 Python 脚本。一旦 LLM 生成了代码，代码块的右上角就会出现一个`运行`按钮。点击此按钮将使用 Pyodide 执行代码。要在代码块底部显示结果，请确保代码中至少有一个 print 语句来显示结果。

## 使用 Python 代码执行的提示

* 编写 Python 代码时，请记住代码将在 Pyodide 环境中执行。在请求代码时提到"Pyodide 环境"，可以让 LLM 了解这一点。
* 研究 Pyodide 文档以了解环境的功能和限制。
* 尝试不同的库和脚本，探索 Open WebUI 中 Python 代码执行的可能性。

## Pyodide 文档

* [Pyodide 文档](https://pyodide.org/en/stable/)

## 代码示例

这是一个可以使用 Pyodide 执行的简单 Python 脚本示例：

```python
import pandas as pd

# 创建示例 DataFrame
data = {'Name': ['John', 'Anna', 'Peter'], 
        'Age': [28, 24, 35]}
df = pd.DataFrame(data)

# 打印 DataFrame
print(df)
```

此脚本将使用 pandas 创建一个示例 DataFrame，并在聊天中的代码块下方打印它。

## 扩展支持的库列表

想要突破可能性的界限？要扩展支持的库列表，请按照以下步骤操作：

1. **复刻 Pyodide 仓库**以创建您自己的版本。
2. 从 Pyodide 内的现有包列表中**选择新包**，或探索 Open WebUI 当前缺少的高质量包。
3. 将新包**集成**到您的复刻仓库中，以解锁更多可能性。
