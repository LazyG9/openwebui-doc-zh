---
sidebar_position: 6
title: "🎨 图像生成"
---

:::warning
本教程来自社区贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据特定用例自定义 Open WebUI 的示例。想要贡献？请查看贡献教程。
:::

# 🎨 图像生成

Open WebUI 支持通过三个后端进行图像生成：**AUTOMATIC1111**、**ComfyUI** 和 **OpenAI DALL·E**。本指南将帮助您设置和使用这些选项。

## AUTOMATIC1111

Open WebUI 支持通过 **AUTOMATIC1111** [API](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API) 生成图像。以下是使用步骤：

### 基础设置

1. 确保您已安装 [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
2. 使用以下参数启动 AUTOMATIC1111 以启用 API 访问：

   ```
   ./webui.sh --api --listen
   ```

3. 如果使用 Docker 安装 WebUI 并预设环境变量，请使用以下命令：

   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e AUTOMATIC1111_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 配置 Open WebUI 与 AUTOMATIC1111

1. 在 Open WebUI 中，进入 **管理面板** > **设置** > **图像** 菜单
2. 将 `图像生成引擎` 设置为 `默认 (Automatic1111)`
3. 在 API URL 字段中，输入 AUTOMATIC1111 的访问地址：

   ```
   http://<your_automatic1111_address>:7860/
   ```

   如果您在同一台机器上运行 Docker 版本的 Open WebUI 和 AUTOMATIC1111，请使用 `http://host.docker.internal:7860/` 作为地址

## ComfyUI

ComfyUI 提供了另一种管理和使用图像生成模型的界面。您可以从其 [GitHub 页面](https://github.com/comfyanonymous/ComfyUI) 了解更多信息或下载。以下是将 ComfyUI 与其他工具配合使用的设置说明。

### 基础设置

1. 从 [GitHub](https://github.com/comfyanonymous/ComfyUI) 下载并解压 ComfyUI 到指定目录
2. 使用以下命令启动 ComfyUI：

   ```
   python main.py
   ```

   如果您的显卡显存较小，可以使用以下命令减少内存占用：

   ```
   python main.py --lowvram
   ```

3. 如果使用 Docker 安装 WebUI 并预设环境变量，请使用以下命令：

   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e COMFYUI_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 配置 Open WebUI 与 ComfyUI

#### 设置 FLUX.1 模型

1. **模型检查点**：

* 从 [black-forest-labs HuggingFace 页面](https://huggingface.co/black-forest-labs) 下载 `FLUX.1-schnell` 或 `FLUX.1-dev` 模型
* 将模型检查点放在 ComfyUI 的 `models/checkpoints` 和 `models/unet` 目录中。您也可以创建这两个目录之间的符号链接，确保它们包含相同的模型检查点

2. **VAE 模型**：

* 从[这里](https://huggingface.co/black-forest-labs/FLUX.1-schnell/blob/main/ae.safetensors)下载 `ae.safetensors` VAE
* 将其放在 ComfyUI 的 `models/vae` 目录中

3. **CLIP 模型**：

* 从[这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main)下载 `clip_l.safetensors`
* 将其放在 ComfyUI 的 `models/clip` 目录中

4. **T5XXL 模型**：

* 从[这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main)下载 `t5xxl_fp16.safetensors` 或 `t5xxl_fp8_e4m3fn.safetensors` 模型
* 将其放在 ComfyUI 的 `models/clip` 目录中

要将 ComfyUI 集成到 Open WebUI 中，请按照以下步骤操作：

#### 步骤 1：配置 Open WebUI 设置

1. 进入 Open WebUI 的**管理面板**
2. 点击**设置**，然后选择**图像**标签
3. 在`图像生成引擎`中选择 `ComfyUI`
4. 在 **API URL** 中输入 ComfyUI 的访问地址：`http://<your_comfyui_address>:8188/`
   * 将环境变量 `COMFYUI_BASE_URL` 设置为此地址，确保设置持久化

#### 步骤 2：验证连接并启用图像生成

1. 确保 ComfyUI 正在运行，并已成功连接到 Open WebUI。没有成功连接将无法继续操作
2. 连接验证成功后，开启**图像生成（实验性）**功能，此时会显示更多选项
3. 继续执行步骤 3 完成最终配置

#### 步骤 3：配置 ComfyUI 设置并导入工作流

1. 在 ComfyUI 中启用开发者模式：找到**队列提示**按钮上方的齿轮图标，开启`开发者模式`
2. 使用`保存（API 格式）`按钮导出工作流。如果操作正确，将下载一个名为 `workflow_api.json` 的文件
3. 返回 Open WebUI，点击**点击此处上传 workflow.json 文件**按钮
4. 选择刚才导出的 `workflow_api.json` 文件，将工作流导入 Open WebUI
5. 导入后，根据工作流节点 ID 配置 `ComfyUI 工作流节点`
6. 将`设置默认模型`设置为您使用的模型文件名，例如 `flux1-dev.safetensors`

:::info
您可能需要在 Open WebUI 的 `ComfyUI 工作流节点`中调整某些`输入键`，使其与工作流中的节点匹配。
例如，可能需要将 `seed` 改为 `noise_seed` 以匹配工作流中的节点 ID。
:::
:::tip
使用 Flux 模型等某些工作流可能需要在 Open WebUI 中填写多个节点 ID。如果一个节点输入字段需要多个 ID，请用逗号分隔（如 `1` 或 `1, 2`）。
:::

6. 点击`保存`应用设置，现在您可以使用集成了 ComfyUI 的 Open WebUI 进行图像生成了！

完成上述步骤后，ComfyUI 就已经与 Open WebUI 集成完成，您可以开始使用 Flux.1 模型生成图像。

### 配置 SwarmUI

SwarmUI 使用 ComfyUI 作为后端。要让 Open WebUI 与 SwarmUI 配合使用，您需要在 `ComfyUI Base URL` 后添加 `ComfyBackendDirect`。同时，您还需要配置 SwarmUI 的局域网访问。完成这些设置后，配置 SwarmUI 与 Open WebUI 的步骤与上面的[步骤一：配置 Open WebUI 设置](https://github.com/open-webui/docs/edit/main/docs/features/images.md#step-1-configure-open-webui-settings)相同。
![使用局域网访问安装 SwarmUI](https://github.com/user-attachments/assets/a6567e13-1ced-4743-8d8e-be526207f9f6)

#### SwarmUI API URL

ComfyUI Base URL 的格式应为：`http://<your_swarmui_address>:7801/ComfyBackendDirect`

## OpenAI DALL·E

Open WebUI 还支持通过 **OpenAI DALL·E API** 生成图像。您可以选择使用 DALL·E 2 或 DALL·E 3，每个版本支持不同的图像尺寸。

### 基础设置

1. 从 OpenAI 获取 [API 密钥](https://platform.openai.com/api-keys)

### 配置 Open WebUI

1. 在 Open WebUI 中，进入 **管理面板** > **设置** > **图像** 菜单
2. 将 `图像生成引擎` 设置为 `Open AI (Dall-E)`
3. 输入您的 OpenAI API 密钥
4. 选择要使用的 DALL·E 模型。注意不同模型支持的图像尺寸不同：
   * **DALL·E 2**：支持 `256x256`、`512x512` 或 `1024x1024`
   * **DALL·E 3**：支持 `1024x1024`、`1792x1024` 或 `1024x1792`

### Azure OpenAI

目前不支持直接使用 Azure OpenAI Dall-E，但您可以[设置 LiteLLM 代理](https://litellm.vercel.app/docs/image_generation)，它与 `Open AI (Dall-E)` 图像生成引擎兼容。

## 使用图像生成

![图像生成教程](/images/tutorial_image_generation.png)

1. 首先使用文本生成模型编写图像生成提示词
2. 响应完成后，点击图片图标即可生成图像
3. 图像生成完成后会自动在聊天中显示

:::tip
    您也可以编辑大语言模型的响应，将自己的图像生成提示词作为消息发送，
    而不必使用模型提供的原始响应。
:::
