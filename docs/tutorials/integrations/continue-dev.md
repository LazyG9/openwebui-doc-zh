---
sidebar_position: 13
title: "⚛️ Continue.dev VSCode 扩展与 Open WebUI 集成指南"
---

:::warning
注意：本教程来自社区贡献，不由 Open WebUI 团队官方支持。它仅用于演示如何根据具体需求自定义 Open WebUI。如果您也想为社区做出贡献，欢迎参考贡献指南。
:::

# Continue.dev VSCode 扩展与 Open WebUI 的集成指南

### 安装扩展程序

首先，请访问 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Continue.continue) 下载并安装 VSCode 的 Continue.dev 扩展。

安装完成后，您会在 VSCode 的侧边栏看到一个名为 'continue' 的新标签页。

打开该标签页，在右下角找到设置图标（呈齿轮状）并点击它。

点击后，VSCode 编辑器将自动打开 `config.json` 配置文件。

接下来，我们将在这个配置文件中设置 Continue 以使用 Open WebUI。

---

需要注意的是，目前 'ollama' 提供程序不支持身份验证功能，因此我们无法直接将其与 Open WebUI 配合使用。

不过好在 Ollama 和 Open WebUI 都支持 OpenAI API 规范。您可以查看 Ollama 的[这篇博客文章](https://ollama.com/blog/openai-compatibility)了解更多详情。

作为替代方案，我们可以配置 Continue 使用 openai 提供程序，这样就能利用 Open WebUI 的身份验证令牌了。

---

## 详细配置步骤

在 `config.json` 配置文件中，您只需要关注并修改以下几个关键选项：

### 1. 设置 API 提供程序

将提供程序设置为 openai：

```json
"provider": "openai"
```

### 2. 配置 API 基础地址

设置您的 Open Web UI 访问地址：

```json
"apiBase": "http://localhost:3000/" #如果您是按照入门指南使用 Docker 部署的
```

### 3. 设置 API 密钥

```json
"apiKey": "sk-79970662256d425eb274fc4563d4525b" # 请替换为您自己的 API 密钥
```

您可以通过以下路径获取 API 密钥：
Open WebUI -> 设置 -> 账户 -> API 密钥

请复制以 "sk-" 开头的 API 密钥字符串。

## 完整配置示例

以下是一个使用 Open WebUI（通过 openai 提供程序）的完整 config.json 配置示例，这里使用了 Granite Code 模型。
请注意：使用前需要确保已经在您的 ollama 实例中下载了相应的模型。

```json
{
  "models": [
    {
      "title": "Granite Code",
      "provider": "openai",
      "model": "granite-code:latest",
      "useLegacyCompletionsEndpoint": false,
      "apiBase": "http://YOUROPENWEBUI/ollama/v1",
      "apiKey": "sk-YOUR-API-KEY"
    }
  ],
  "customCommands": [
    {
      "name": "test",
      "prompt": "{{{ input }}}\n\nWrite a comprehensive set of unit tests for the selected code. It should setup, run tests that check for correctness including important edge cases, and teardown. Ensure that the tests are complete and sophisticated. Give the tests just as chat output, don't edit any file.",
      "description": "Write unit tests for highlighted code"
    }
  ],
  "tabAutocompleteModel": {
    "title": "Granite Code",
    "provider": "openai",
    "model": "granite-code:latest",
    "useLegacyCompletionsEndpoint": false,
    "apiBase": "http://localhost:3000/ollama/v1",
    "apiKey": "sk-YOUR-API-KEY"
  }
}
```

完成配置后，保存 `config.json` 文件即可。

现在，您应该能在 Continue 标签页的模型选择下拉列表中看到配置的模型了。

选择该模型后，您就可以通过 Open WebUI（以及您配置的任何[处理管道](/pipelines)）开始 AI 辅助编程了。

虽然理论上任何模型都可以工作，但我们强烈建议使用专门针对代码处理优化的模型。您可以根据需要配置多个不同的模型。

如需了解 Continue 的更多配置选项，请参考[官方文档](https://docs.continue.dev/reference/Model%20Providers/openai)。
