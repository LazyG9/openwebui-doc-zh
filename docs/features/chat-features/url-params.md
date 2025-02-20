---
sidebar_position: 5
title: "🔗 URL 参数"
---

在 Open WebUI 中，会话可以通过各种 URL 参数进行自定义配置。这些参数让您能够为每个会话设置特定配置、启用功能和定义模型设置。通过 URL 直接配置，为每个会话提供了灵活性和精确控制。

## URL 参数概览

下表列出了可用的 URL 参数、其功能和使用示例。

| **参数**      | **描述**                                                                  | **示例**                          |
|-----------------------|----------------------------------------------------------------------------------|--------------------------------------------------------|
| `models`           | 指定要使用的模型（以逗号分隔的列表形式）                     | `/?models=model1,model2`         |
| `model`            | 指定单个模型用于会话                       | `/?model=model1`                 |
| `youtube`          | 指定需要转写的 YouTube 视频 ID                 | `/?youtube=VIDEO_ID`             |
| `web-search`       | 设置为 `true` 时启用网络搜索功能                              | `/?web-search=true`              |
| `tools` 或 `tool-ids` | 指定要启用的工具 ID（以逗号分隔的列表形式）          | `/?tools=tool1,tool2`            |
| `call`             | 设置为 `true` 时启用通话叠加层                                        | `/?call=true`                    |
| `q`                | 设置会话的初始提示词                                   | `/?q=Hello%20there`              |
| `temporary-chat`   | 设置为 `true` 时创建临时会话            | `/?temporary-chat=true`          |

### 1. **模型和模型选择**

- **描述**：通过 `models` 和 `model` 参数可以指定会话使用的[语言模型](/features/workspace/models.md)
- **配置方式**：使用 `models` 可指定多个模型，`model` 则用于单个模型
- **示例**：
  - `/?models=model1,model2` – 使用 `model1` 和 `model2` 初始化会话
  - `/?model=model1` – 将 `model1` 设为唯一使用的模型

### 2. **YouTube 转写**

- **描述**：`youtube` 参数用于接收 YouTube 视频 ID，实现视频内容转写
- **配置方式**：直接使用 YouTube 视频的 ID
- **示例**：`/?youtube=VIDEO_ID`
- **功能**：自动为指定的 YouTube 视频启动转写功能

### 3. **网络搜索**

- **描述**：启用 `web-search` 参数可使用[网络搜索](/category/-web-search)功能
- **配置方式**：将参数值设为 `true`
- **示例**：`/?web-search=true`
- **功能**：启用后，会话可以获取并使用网络搜索结果

### 4. **工具选择**

- **描述**：使用 `tools` 或 `tool-ids` 参数来指定要启用的[工具](/features/plugin/tools)
- **配置方式**：以逗号分隔的工具 ID 列表
- **示例**：`/?tools=tool1,tool2` 或 `/?tool-ids=tool1,tool2`
- **功能**：激活指定的工具供用户使用

### 5. **通话叠加层**

- **描述**：`call` 参数用于启用视频或通话叠加界面
- **配置方式**：设置参数值为 `true`
- **示例**：`/?call=true`
- **功能**：启用通话界面，支持实时转写和视频输入等功能

### 6. **初始提示词**

- **描述**：通过 `q` 参数设置会话的初始提示词
- **配置方式**：直接输入提示词文本
- **示例**：`/?q=Hello%20there`
- **功能**：会话启动时自动发送指定的提示词

### 7. **临时会话**

- **描述**：使用 `temporary-chat` 参数创建临时会话，不保存历史记录
- **配置方式**：将参数值设为 `true`
- **示例**：`/?temporary-chat=true`
- **功能**：创建一次性会话，不保存历史记录也不应用持久化设置

<details>
<summary>使用案例</summary>
:::tip **临时会话示例**
如果用户需要进行一次性对话而不想保存聊天记录，可以通过在 URL 中添加 `temporary-chat=true` 参数来实现。这样可以创建一个适合临时使用的会话环境。
:::
</details>

## 组合使用多个参数

这些 URL 参数可以组合使用，创建高度个性化的会话配置。例如：

```bash
/chat?models=model1,model2&youtube=VIDEO_ID&web-search=true&tools=tool1,tool2&call=true&q=Hello%20there&temporary-chat=true
```

这个 URL 将会：

- 使用 `model1` 和 `model2` 初始化会话
- 启用 YouTube 转写、网络搜索和指定工具
- 显示通话叠加层
- 设置 "Hello there" 作为初始提示词
- 将会话标记为临时，不保存历史记录
