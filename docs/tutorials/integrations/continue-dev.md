---
sidebar_position: 13
title: "⚛️ Open WebUI 集成 Continue.dev VSCode 扩展教程"
---

:::warning
本教程来自社区贡献，未经 OpenWebUI 官方团队验证。仅供演示如何根据具体需求自定义 OpenWebUI 使用。如果您也想贡献内容，请参考贡献指南。
:::

# Open WebUI 集成 Continue.dev VSCode 扩展教程

### 安装扩展

请前往 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Continue.continue)（VS Code 应用商店）下载并安装 VSCode 扩展

安装完成后，您将在 VSCode 侧边栏看到 'continue' 标签页。

打开该标签页，在右下角找到设置图标（齿轮样式图标）。

点击设置图标后，编辑器将自动打开 `config.json` 配置文件。

您可以在此文件中配置 Continue 以接入 Open WebUI。

---

目前，由于 'ollama' provider（服务提供者）不支持身份验证功能，我们无法直接将其与 Open WebUI 配合使用。

但是，Ollama 和 Open WebUI 都支持 OpenAI API 规范。您可以查看 Ollama 的[这篇博客文章](https://ollama.com/blog/openai-compatibility)了解详情。

我们可以通过配置 Continue 使用 OpenAI provider，并结合 Open WebUI 的身份验证令牌来实现集成。

---

## 配置说明

在 `config.json` 中，您只需要配置以下几项内容：

### 设置 provider 为 openai

```json
"provider": "openai"
```

### 配置 apiBase

将其设置为您的 Open Web UI 访问地址：

```json
"apiBase": "http://localhost:3000/" # Docker 默认安装地址
```

### 设置 apiKey

```json
"apiKey": "sk-79970662256d425eb274fc4563d4525b" # 替换为您的 API 密钥
```

您可以在 Open WebUI 的 设置 -> 账户 -> API 密钥 中生成并获取 API 密钥

请复制以 "sk-" 开头的 API 密钥

## 配置文件示例

以下是一个基础的 config.json 配置示例，展示了如何通过 OpenAI provider 接入 Open WebUI，并使用 Granite Code 模型：
注意：使用前请确保已在 Ollama 实例中下载相应模型。

```json
{
  "models": [
    {
      "title": "Granite Code",
      "provider": "openai",
      "model": "granite-code:latest",
      "useLegacyCompletionsEndpoint": false,
      "apiBase": "http://YOUROPENWEBUI/ollama/v1",  # 替换为您的 Open WebUI 地址
      "apiKey": "sk-YOUR-API-KEY"  # 替换为您的 API 密钥
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
    "apiBase": "http://localhost:3000/ollama/v1",  # 本地默认地址
    "apiKey": "sk-YOUR-API-KEY"  # 替换为您的 API 密钥
  }
}
```

保存 `config.json` 后，配置就完成了！

现在您可以在 Continue 标签页的模型选择下拉菜单中看到配置的模型。

选择该模型后，您就可以通过 Open WebUI（以及您配置的任何[处理管道](/pipelines)）开始对话了。

您可以按照上述方式配置多个模型。虽然理论上所有模型都可以使用，但我们建议选择专门用于代码处理的模型。

更多 Continue 配置详情，请参考 [Continue 官方文档](https://docs.continue.dev/reference/Model%20Providers/openai)（英文）
