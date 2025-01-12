---
sidebar_position: 13
title: "⚛️ 在 Open WebUI 中使用 Continue.dev VSCode 扩展"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 将 Continue.dev VSCode 扩展与 Open WebUI 集成

### 下载扩展

您可以在 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=Continue.continue) 下载 VSCode 扩展

安装完成后，您现在应该在侧边栏中看到一个 'continue' 标签页。

打开它。在右下角您应该看到一个设置图标（看起来像齿轮）。

点击设置图标后，编辑器中应该会打开一个 `config.json` 文件。

在这里您可以配置 continue 以使用 Open WebUI。

---

目前 'ollama' 提供程序不支持身份验证，所以我们不能将此提供程序与 Open WebUI 一起使用。

但是 Ollama 和 Open WebUI 都兼容 OpenAI API 规范。您可以在[这里](https://ollama.com/blog/openai-compatibility)查看 Ollama 的博客文章了解这一点。

我们仍然可以设置 Continue 使用 openai 提供程序，这将允许我们使用 Open WebUI 的身份验证令牌。

---

## 配置

在 `config.json` 中，您只需要添加/更改以下选项。

### 将提供程序更改为 openai

```json
"provider": "openai"
```

### 添加或更新 apiBase

将其设置为您的 Open Web UI 域名。

```json
"apiBase": "http://localhost:3000/" #如果您按照入门指南使用 Docker
```

### 添加 apiKey

```json
"apiKey": "sk-79970662256d425eb274fc4563d4525b" # 替换为您的 API 密钥
```

您可以在 Open WebUI -> Settings -> Account -> API Keys 中找到并生成您的 api 密钥

您需要复制 "API Key"（以 sk- 开头）

## 配置示例

这是一个使用 Open WebUI 通过 openai 提供程序的 config.json 基本示例。使用 Granite Code 作为模型。
确保您事先将模型拉取到您的 ollama 实例中。

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
      "prompt": "{{{ input }}}\n\n为选定的代码编写一套全面的单元测试。它应该包括设置、运行测试以检查正确性（包括重要的边缘情况）和清理。确保测试是完整和复杂的。仅作为聊天输出给出测试，不要编辑任何文件。",
      "description": "为高亮显示的代码编写单元测试"
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

保存您的 `config.json`，就这样！

您现在应该在 Continue 标签页的模型选择中看到您的模型。

选择它，您现在应该通过 Open WebUI（和/或您设置的任何[管道](/pipelines)）进行聊天了。

您可以为任意数量的想要使用的模型执行此操作，虽然任何模型都应该可以工作，但您应该使用专为代码设计的模型。

有关其他 continue 配置，请参阅 continue 文档，[Continue 文档](https://docs.continue.dev/reference/Model%20Providers/openai)
