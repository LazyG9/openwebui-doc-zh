---
sidebar_position: 4
title: "🗨️ 聊天分享"
---

### 启用社区分享

要启用社区分享，请按照以下步骤操作：

1. 以**管理员**身份导航到**管理面板**页面。
2. 点击**设置**标签。
3. 在**常规**设置标签中打开**启用社区分享**开关。

:::note
**注意：**只有管理员可以切换**启用社区分享**选项。如果此选项关闭，用户和管理员将看不到**分享到 Open WebUI 社区**选项来分享他们的聊天。用户必须由管理员启用社区分享才能将聊天分享到 Open WebUI 社区。
:::

这将为您的 Open WebUI 实例的所有用户启用社区分享。

### 分享聊天

要分享聊天：

1. 选择您想要分享的聊天对话。
2. 将鼠标指针悬停在所需聊天上方时，点击出现的 3 个点。
3. 然后点击**分享**选项。
4. 选择**分享到 Open WebUI 社区**（如果管理员已打开**启用社区分享**）或**复制链接**。

#### 分享到 Open WebUI 社区

当您选择`分享到 Open WebUI 社区`时：

* 将打开一个新标签页，允许您将聊天作为快照上传到 Open WebUI 社区网站（https://openwebui.com/chats/upload）。
* 您可以通过选择以下访问设置来控制谁可以查看您上传的聊天：
  * **私密**：只有您可以访问此聊天。
  * **公开**：互联网上的任何人都可以查看聊天快照中显示的消息。
  * **公开，完整历史**：互联网上的任何人都可以查看您聊天的完整重新生成历史。

:::note
注意：您可以随时从您的 openwebui.com 账户更改在社区平台上共享聊天的权限级别。

**目前，社区网站上的共享聊天无法通过搜索找到。但是，计划在未来的更新中允许公开聊天在权限设置为`公开`或`公开，完整历史`时可被公众发现。**
:::

分享到社区平台网站的聊天示例：https://openwebui.com/c/iamg30/5e3c569f-905e-4d68-a96d-8a99cc65c90f

#### 复制分享链接

当您选择`复制链接`时，将生成一个可以与他人分享的唯一分享链接。

**重要注意事项：**

* 共享的聊天将只包括创建链接时存在的消息。生成链接后在聊天中发送的任何新消息都不会包括在内，除非删除链接并用新链接更新。
* 生成的分享链接作为生成链接时聊天的静态快照。
* 要查看共享的聊天，用户必须：
  1. 在生成链接的 Open WebUI 实例上拥有账户。
  2. 在该实例上登录到他们的账户。
* 如果用户在未登录的情况下尝试访问共享链接，他们将被重定向到登录页面，登录后才能查看共享的聊天。

### 查看共享的聊天

要查看共享的聊天：

1. 确保您已登录到共享聊天的 Open WebUI 实例的账户。
2. 点击提供给您的共享链接。
3. 聊天将以只读格式显示。
4. 如果共享链接的 Open WebUI 实例的管理员已设置文本转语音，可能会有一个音频按钮可以为您朗读消息（视情况而定）。

### 更新共享的聊天

要更新共享的聊天：

1. 选择您想要分享的聊天对话。
2. 将鼠标指针悬停在所需聊天上方时，点击出现的 3 个点。
3. 点击**分享**选项。
4. 如果您之前已经分享过聊天，**分享聊天**模态框应该看起来不同。

**分享聊天**模态框包括以下选项：

* **之前**：打开新标签页查看之前从分享链接共享的聊天。
* **删除此链接**：删除聊天的共享链接并显示初始分享聊天模态框。
* **分享到 Open WebUI 社区**：打开新标签页访问 https://openwebui.com/chats/upload，聊天已准备好作为快照分享。
* **更新并复制链接**：更新之前共享的聊天链接的聊天快照，并将其复制到您设备的剪贴板。

### 删除共享的聊天

要删除共享的聊天链接：

1. 选择您想要删除共享链接的聊天对话。
2. 将鼠标指针悬停在所需聊天上方时，点击出现的 3 个点。
3. 点击**分享**选项。
4. 如果您之前已经分享过聊天，**分享聊天**模态框应该看起来不同。
5. 点击**删除此链接**。

删除后，共享链接将不再有效，用户将无法查看共享的聊天。

:::note
**注意：**分享到社区平台的聊天无法删除。要更改分享到社区平台的聊天的访问级别：
:::

1. 在 openwebui.com 登录您的 Open WebUI 账户。
2. 点击网站右上角的账户用户名。
3. 点击**聊天**。
4. 点击您想要更改权限访问的聊天。
5. 滚动到聊天底部并更新其权限级别。
6. 点击**更新聊天**按钮。
