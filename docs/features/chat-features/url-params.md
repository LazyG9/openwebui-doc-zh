---
sidebar_position: 5
title: "🔗 网址参数"
---

在 Open WebUI 中，您可以通过各种网址参数来自定义聊天对话。这些参数让您能够为每个聊天设置特定配置、启用功能和定义模型设置。通过网址直接控制单个聊天对话的方式既灵活又方便。

## 网址参数概览

下表列出了所有可用的网址参数、其功能和使用示例。

| **参数**      | **描述**                                                                  | **示例**                          |
|-----------------------|----------------------------------------------------------------------------------|--------------------------------------------------------|
| `models`           | 指定要使用的模型（多个模型用逗号分隔）                     | `/?models=model1,model2`         |
| `model`            | 指定要使用的单个模型                       | `/?model=model1`                 |
| `youtube`          | 指定需要转录的 YouTube 视频 ID                 | `/?youtube=VIDEO_ID`             |
| `web-search`       | 设置为 `true` 时启用在线搜索功能                              | `/?web-search=true`              |
| `tools` 或 `tool-ids` | 指定要启用的工具 ID（多个工具用逗号分隔）          | `/?tools=tool1,tool2`            |
| `call`             | 设置为 `true` 时启用通话界面                                        | `/?call=true`                    |
| `q`                | 设置对话的初始问题或提示词                                   | `/?q=Hello%20there`              |
| `temporary-chat`   | 设置为 `true` 时创建临时对话（不保存记录）            | `/?temporary-chat=true`          |

### 1. **选择模型**

- **描述**：通过 `models` 和 `model` 参数来选择要使用的[语言模型](/features/workspace/models.md)。
- **设置方法**：使用 `models` 可以选择多个模型，使用 `model` 则选择单个模型。
- **示例**：
  - `/?models=model1,model2` – 同时启用两个模型 `model1` 和 `model2`
  - `/?model=model1` – 仅使用 `model1` 一个模型

### 2. **YouTube 视频转录**

- **描述**：使用 `youtube` 参数可以直接转录 YouTube 视频内容。
- **设置方法**：输入要转录的 YouTube 视频 ID。
- **示例**：`/?youtube=VIDEO_ID`
- **功能**：自动转录指定的 YouTube 视频内容。

### 3. **在线搜索**

- **描述**：启用 `web-search` 后可以使用[在线搜索](/tutorials/web_search)功能。
- **设置方法**：将参数值设为 `true`。
- **示例**：`/?web-search=true`
- **功能**：启用后，AI 可以搜索网络信息来辅助回答。

### 4. **启用工具**

- **描述**：通过 `tools` 或 `tool-ids` 参数来选择要使用的[辅助工具](/features/plugin/tools)。
- **设置方法**：用逗号分隔多个工具 ID。
- **示例**：`/?tools=tool1,tool2` 或 `/?tool-ids=tool1,tool2`
- **功能**：激活指定的工具供对话中使用。

### 5. **通话界面**

- **描述**：`call` 参数用于启用视频或语音通话界面。
- **设置方法**：将参数值设为 `true`。
- **示例**：`/?call=true`
- **功能**：显示通话界面，支持实时语音转录和视频输入等功能。

### 6. **设置初始问题**

- **描述**：使用 `q` 参数可以预设对话的第一个问题。
- **设置方法**：直接输入问题文本。
- **示例**：`/?q=Hello%20there`
- **功能**：打开对话时自动发送预设的问题。

### 7. **临时对话**

- **描述**：使用 `temporary-chat` 参数可以创建不保存记录的临时对话。
- **设置方法**：将参数值设为 `true`。
- **示例**：`/?temporary-chat=true`
- **功能**：创建一个不保存历史记录的一次性对话。

<details>
<summary>使用案例</summary>
:::tip **创建临时对话**
如果您想快速进行一次性对话而不想保存记录，只需在网址中添加 `temporary-chat=true` 参数。这样创建的对话不会保存到历史记录中，非常适合临时使用。
:::
</details>

## 组合使用多个参数

您可以组合使用这些参数来创建功能丰富的自定义对话。例如：

```bash
/chat?models=model1,model2&youtube=VIDEO_ID&web-search=true&tools=tool1,tool2&call=true&q=Hello%20there&temporary-chat=true
```

这个网址将会：

- 启用两个模型：`model1` 和 `model2`
- 开启 YouTube 视频转录功能
- 启用在线搜索功能
- 激活指定的辅助工具
- 显示通话界面
- 自动发送 "Hello there" 作为第一个问题
- 创建一个不保存记录的临时对话
