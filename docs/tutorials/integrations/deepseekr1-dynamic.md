---
sidebar_position: 1
title: "🐋 使用 Llama.cpp 运行 DeepSeek R1 动态量化模型"
---

非常感谢 **UnslothAI** 团队的杰出贡献！通过他们的不懈努力，我们现在可以在 **Llama.cpp** 上运行完整版 **DeepSeek-R1** 671B 参数模型的动态 1.58 位量化版本（压缩至仅 131GB）！最令人兴奋的是，您不再需要昂贵的企业级 GPU 或服务器 — 现在可以在个人电脑上运行这个模型（虽然在大多数消费级硬件上运行速度会较慢）。

:::note
请注意，Ollama 平台上唯一的官方 **DeepSeek-R1** 模型是这里的 **671B 版本**：[https://ollama.com/library/deepseek-r1:671b](https://ollama.com/library/deepseek-r1:671b)。其他版本均为**蒸馏**模型。
:::

本指南将重点介绍如何使用集成了 **Open WebUI** 的 **Llama.cpp** 来运行**完整的 DeepSeek-R1 动态 1.58 位量化模型**。在本教程中，我们将以 **M4 Max + 128GB RAM** 配置为例进行演示。您可以根据自己的硬件配置相应调整设置。

---

## 步骤 1：安装 Llama.cpp

您可以选择以下两种方式之一：
- [下载预编译的二进制文件](https://github.com/ggerganov/llama.cpp/releases)（推荐新手使用）
- **或者自行编译**：参考[Llama.cpp 编译指南](https://github.com/ggerganov/llama.cpp/blob/master/docs/build.md)（适合高级用户）

## 步骤 2：下载 UnslothAI 优化的模型

访问 [Unsloth 的 Hugging Face 页面](https://huggingface.co/unsloth/DeepSeek-R1-GGUF)，下载适合您设备的 DeepSeek-R1 **动态量化版本**。本教程将使用 **1.58 位（131GB）**版本，这是一个经过高度优化且保持出色性能的版本。

:::tip
💡 工作目录提示
请特别注意您的"工作目录"位置 — 这是您的 Python 脚本或终端会话运行的地方。模型文件会默认下载到该目录的子文件夹中，因此请确保记住这个路径！

示例路径：
- Windows：`C:\Users\用户名\Documents\projects\DeepSeek-R1-GGUF`
- macOS/Linux：`/Users/用户名/Documents/projects/DeepSeek-R1-GGUF`
:::

想要深入了解 UnslothAI 的开发过程以及这些动态量化版本为何如此高效，请阅读他们的技术博客：[UnslothAI DeepSeek R1 动态量化技术解析](https://unsloth.ai/blog/deepseekr1-dynamic)。

以下是通过代码下载模型的方法：
```python
# 运行前请先安装必要的依赖：
# pip install huggingface_hub hf_transfer

from huggingface_hub import snapshot_download

snapshot_download(
    repo_id = "unsloth/DeepSeek-R1-GGUF",  # 指定 Hugging Face 仓库
    local_dir = "DeepSeek-R1-GGUF",         # 模型将下载到此目录
    allow_patterns = ["*UD-IQ1_S*"],        # 仅下载 1.58 位版本
)
```

下载完成后，您的目录结构应如下所示：
```
DeepSeek-R1-GGUF/
├── DeepSeek-R1-UD-IQ1_S/
│   ├── DeepSeek-R1-UD-IQ1_S-00001-of-00003.gguf
│   ├── DeepSeek-R1-UD-IQ1_S-00002-of-00003.gguf
│   ├── DeepSeek-R1-UD-IQ1_S-00003-of-00003.gguf
```

:::info
🛠️ 路径配置说明
在后续步骤中，请根据您的实际目录结构**更新路径**。路径示例：
- Windows：`C:\Users\tim\Downloads\DeepSeek-R1-GGUF\DeepSeek-R1-UD-IQ1_S\DeepSeek-R1-UD-IQ1_S-00001-of-00003.gguf`
- macOS/Linux：`/Users/tim/Downloads/DeepSeek-R1-GGUF/DeepSeek-R1-UD-IQ1_S/DeepSeek-R1-UD-IQ1_S-00001-of-00003.gguf`
:::

## 步骤 3：安装并运行 Open WebUI

如果您还未安装 **Open WebUI**，不用担心！安装过程非常简单。只需按照[Open WebUI 官方文档](https://docs.openwebui.com/)的指引操作即可。安装完成后启动应用程序 — 稍后我们将把它与 DeepSeek-R1 模型连接起来。

## 步骤 4：使用 Llama.cpp 部署模型

模型下载完成后，接下来我们要使用 **Llama.cpp 的服务器模式**来运行它。开始之前的准备工作：

1. **找到 `llama-server` 程序**
   如果您是从源代码编译的（参考步骤 1），`llama-server` 程序位于 `llama.cpp/build/bin` 目录中。使用以下命令进入该目录：
   ```bash
   # Windows 系统
   cd C:\path\to\llama.cpp\build\bin
   
   # macOS/Linux 系统
   cd ~/Documents/workspace/llama.cpp/build/bin
   ```

2. **配置模型路径**
   使用步骤 2 中下载的 GGUF 文件的完整路径。运行模型时，需要指定分割 GGUF 文件中的第一个文件（例如：`DeepSeek-R1-UD-IQ1_S-00001-of-00003.gguf`）。

启动服务器的命令如下：
```bash
./llama-server \
    --model /[您的目录]/DeepSeek-R1-GGUF/DeepSeek-R1-UD-IQ1_S/DeepSeek-R1-UD-IQ1_S-00001-of-00003.gguf \
    --port 10000 \
    --ctx-size 1024 \
    --n-gpu-layers 40
```

:::tip
🔑 **硬件配置参数说明：**

- **`--model`：** 替换 `/[您的目录]/` 为实际的模型文件路径
- **`--port`：** 服务端口号，默认为 `8080`，可根据需要更改
- **`--ctx-size`：** 上下文窗口大小（Token 数量）
  - 8GB 显存：建议设置 1024
  - 16GB 显存：可以尝试 2048
  - 24GB 及以上：可以设置 4096
- **`--n-gpu-layers`：** GPU 加速层数，根据显存大小调整
  - 8GB 显存：建议 20-30 层
  - 16GB 显存：可以设置 35-45 层
  - 24GB 及以上：可以尝试 50 层以上
:::

配置示例（以 Windows 系统为例）：
```bash
./llama-server \
    --model C:\Users\tim\Documents\workspace\DeepSeek-R1-GGUF\DeepSeek-R1-UD-IQ1_S\DeepSeek-R1-UD-IQ1_S-00001-of-00003.gguf \
    --port 10000 \
    --ctx-size 1024 \
    --n-gpu-layers 40
```

服务器启动后，将在以下地址提供 **OpenAI 兼容的 API** 服务：
```
http://127.0.0.1:10000
```

:::info
🖥️ **服务器运行状态**

![服务器运行截图](/images/tutorials/deepseek/serve.png)

运行命令后，您会看到服务器已在端口 10000 上启动的确认消息。
:::

⚠️ 重要提示：请保持此终端窗口处于运行状态，因为它为后续操作提供必要的模型服务。

## 步骤 5：配置 Open WebUI 连接

1. 打开 Open WebUI 的**管理设置**面板
2. 进入**连接 > OpenAI 连接**设置
3. 添加新连接，填写以下信息：
   - URL：
     - 本地运行：`http://127.0.0.1:10000/v1`
     - Docker 环境：`http://host.docker.internal:10000/v1`
   - API 密钥：`none`（无需密钥）

:::info
🖥️ **连接配置界面**

![连接配置截图](/images/tutorials/deepseek/connection.png)

完成配置后，确认服务器状态显示正常运行。
:::

配置保存后，您就可以开始通过 Open WebUI 使用 **DeepSeek-R1** 模型了！🎉

---

## 使用示例：模型对话

现在您可以通过 Open WebUI 的聊天界面与 **DeepSeek-R1 动态 1.58 位模型**进行对话了。

![对话示例截图](/images/tutorials/deepseek/response.png)

---

## 重要注意事项

- **性能说明：**
  - 在个人设备上运行 131GB 的 DeepSeek-R1 模型会比较慢
  - 即使在 M4 Max（128GB RAM）上，推理速度也不算快
  - 但能在个人设备上运行如此大的模型，已经证明了 UnslothAI 优化的成功

- **硬件要求：**
  - 推荐配置：
    - 系统内存：32GB 以上
    - 显存：16GB 以上
    - 存储空间：至少 200GB 可用空间
  - 最低配置：
    - 系统内存：16GB
    - 显存：8GB
    - 存储空间：150GB

---

特别感谢 **UnslothAI** 和 **Llama.cpp** 团队的贡献，让我们能够在个人设备上运行 **DeepSeek-R1**（1.58 位版本）这样的大型开源推理模型。虽然在消费级硬件上运行这类模型仍有挑战，但无需依赖大型计算设施就能实现这一目标，这无疑是人工智能民主化的重要里程碑。

⭐ 感谢开源社区不断推动 AI 技术的进步。

祝您使用愉快！🚀
