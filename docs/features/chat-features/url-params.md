---
sidebar_position: 5
title: "🔗 URL 参数"
---

在 Open WebUI 中，聊天会话可以通过各种 URL 参数进行自定义。这些参数允许您在每次聊天的基础上设置特定配置、启用功能和定义模型设置。这种方法直接通过 URL 为单个聊天会话提供了灵活性和控制力。

## URL 参数概述

下表列出了可用的 URL 参数、其功能和使用示例。

| **参数**      | **描述**                                                                  | **示例**                          |
|-----------------------|----------------------------------------------------------------------------------|--------------------------------------------------------|
| `models`           | 指定要使用的模型，以逗号分隔的列表形式。                     | `/?models=model1,model2`         |
| `model`            | 指定用于聊天会话的单个模型。                       | `/?model=model1`                 |
| `youtube`          | 指定要在聊天中转录的 YouTube 视频 ID。                 | `/?youtube=VIDEO_ID`             |
| `web-search`       | 如果设置为 `true`，则启用网页搜索功能。                              | `/?web-search=true`              |
| `tools` 或 `tool-ids` | 指定要在聊天中激活的工具 ID，以逗号分隔的列表形式。          | `/?tools=tool1,tool2`            |
| `call`             | 如果设置为 `true`，则启用通话覆盖。                                        | `/?call=true`                    |
| `q`                | 为聊天设置初始查询或提示。                                   | `/?q=Hello%20there`              |
| `temporary-chat`   | 如果设置为 `true`，则将聊天标记为临时会话。            | `/?temporary-chat=true`          |

### 1. **模型和模型选择**

- **描述**：`models` 和 `model` 参数允许您指定特定聊天会话应使用的[语言模型](/features/workspace/models.md)。
- **如何设置**：您可以使用 `models` 指定多个模型，或使用 `model` 指定单个模型。
- **示例**：
  - `/?models=model1,model2` – 使用 `model1` 和 `model2` 初始化聊天。
  - `/?model=model1` – 将 `model1` 设置为聊天的唯一模型。

### 2. **YouTube 转录**

- **描述**：`youtube` 参数接受 YouTube 视频 ID，使聊天能够转录指定的视频。
- **如何设置**：使用 YouTube 视频 ID 作为此参数的值。
- **示例**：`/?youtube=VIDEO_ID`
- **行为**：这会为提供的 YouTube 视频触发聊天中的转录功能。

### 3. **网页搜索**

- **描述**：启用 `web-search` 允许聊天会话访问[网页搜索](/tutorials/integrations/web_search)功能。
- **如何设置**：将此参数设置为 `true` 以启用网页搜索。
- **示例**：`/?web-search=true`
- **行为**：如果启用，聊天可以将网页搜索结果作为其响应的一部分。

### 4. **工具选择**

- **描述**：`tools` 或 `tool-ids` 参数指定要在聊天中激活的[工具](/features/plugin/tools)。
- **如何设置**：提供以逗号分隔的工具 ID 列表作为参数值。
- **示例**：`/?tools=tool1,tool2` 或 `/?tool-ids=tool1,tool2`
- **行为**：每个工具 ID 都会在会话中匹配并激活，供用户交互。

### 5. **通话覆盖**

- **描述**：`call` 参数在聊天界面中启用视频或通话覆盖。
- **如何设置**：将参数设置为 `true` 以启用通话覆盖。
- **示例**：`/?call=true`
- **行为**：激活通话界面覆盖，允许实时转录和视频输入等功能。

### 6. **初始查询提示**

- **描述**：`q` 参数允许为聊天设置初始查询或提示。
- **如何设置**：将查询或提示文本指定为参数值。
- **示例**：`/?q=Hello%20there`
- **行为**：聊天以指定的提示开始，自动将其作为第一条消息提交。

### 7. **临时聊天会话**

- **描述**：`temporary-chat` 参数将聊天标记为临时会话。这可能会限制保存聊天历史或应用持久设置等功能。
- **如何设置**：将此参数设置为 `true` 以创建临时聊天会话。
- **示例**：`/?temporary-chat=true`
- **行为**：这会启动一个一次性聊天会话，不保存历史记录或应用高级配置。

<details>
<summary>使用案例示例</summary>
:::tip **临时聊天会话**
假设用户想要启动一个不保存历史记录的快速聊天会话。他们可以通过在 URL 中设置 `temporary-chat=true` 来实现。这提供了一个适合一次性交互的一次性聊天环境。
:::
</details>

## 组合使用多个参数

这些 URL 参数可以组合使用，创建高度自定义的聊天会话。例如：

```bash
/chat?models=model1,model2&youtube=VIDEO_ID&web-search=true&tools=tool1,tool2&call=true&q=Hello%20there&temporary-chat=true
```

此 URL 将：

- 使用 `model1` 和 `model2` 初始化聊天。
- 启用 YouTube 转录、网页搜索和指定的工具。
- 显示通话覆盖。
- 设置"Hello there"作为初始提示。
- 将聊天标记为临时，避免保存任何历史记录。
