---
sidebar_position: 6
title: "🎨 图像生成"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何根据您的具体使用场景自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 🎨 图像生成

Open WebUI 支持通过三个后端进行图像生成：**AUTOMATIC1111**、**ComfyUI** 和 **OpenAI DALL·E**。本指南将帮助您设置和使用这些选项。

## AUTOMATIC1111

Open WebUI 支持通过 **AUTOMATIC1111** [API](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API) 生成图像。以下是开始使用的步骤：

### 初始设置

1. 确保您已安装 [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)。
2. 使用额外的参数启动 AUTOMATIC1111 以启用 API 访问：

   ```
   ./webui.sh --api --listen
   ```

3. 对于预设环境变量的 WebUI Docker 安装，使用以下命令：

   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e AUTOMATIC1111_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 设置 Open WebUI 与 AUTOMATIC1111

1. 在 Open WebUI 中，导航至「管理面板」>「设置」>「图像」菜单。
2. 将「图像生成引擎」字段设置为「默认 (Automatic1111)」。
3. 在 API URL 字段中，输入 AUTOMATIC1111 的 API 访问地址：

   ```
   http://<your_automatic1111_address>:7860/
   ```

   如果您在同一主机上运行 Open WebUI 和 AUTOMATIC1111 的 Docker 安装，请使用 `http://host.docker.internal:7860/` 作为地址。

## ComfyUI

ComfyUI 提供了一个用于管理和交互图像生成模型的替代界面。从其 [GitHub 页面](https://github.com/comfyanonymous/ComfyUI)了解更多或下载。以下是将 ComfyUI 与其他工具配合使用的设置说明。

### 初始设置

1. 从 [GitHub](https://github.com/comfyanonymous/ComfyUI) 下载并解压 ComfyUI 软件包到您想要的目录。
2. 要启动 ComfyUI，运行以下命令：

   ```
   python main.py
   ```

   对于显存较小的系统，使用以下参数启动 ComfyUI 以减少内存使用：

   ```
   python main.py --lowvram
   ```

3. 对于预设环境变量的 WebUI Docker 安装，使用以下命令：

   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e COMFYUI_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 设置 Open WebUI 与 ComfyUI

#### 设置 FLUX.1 模型

1. **模型检查点**：

* 从 [black-forest-labs HuggingFace 页面](https://huggingface.co/black-forest-labs)下载 `FLUX.1-schnell` 或 `FLUX.1-dev` 模型。
* 将模型检查点放在 ComfyUI 的 `models/checkpoints` 和 `models/unet` 目录中。或者，您可以在 `models/checkpoints` 和 `models/unet` 之间创建符号链接，以确保两个目录包含相同的模型检查点。

2. **VAE 模型**：

* 从[这里](https://huggingface.co/black-forest-labs/FLUX.1-schnell/blob/main/ae.safetensors)下载 `ae.safetensors` VAE。
* 将其放在 ComfyUI 的 `models/vae` 目录中。

3. **CLIP 模型**：

* 从[这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main)下载 `clip_l.safetensors`。
* 将其放在 ComfyUI 的 `models/clip` 目录中。

4. **T5XXL 模型**：

* 从[这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main)下载 `t5xxl_fp16.safetensors` 或 `t5xxl_fp8_e4m3fn.safetensors` 模型。
* 将其放在 ComfyUI 的 `models/clip` 目录中。

要将 ComfyUI 集成到 Open WebUI 中，请按照以下步骤操作：

#### 步骤 1：配置 Open WebUI 设置

1. 导航至 Open WebUI 中的「管理面板」。
2. 点击「设置」，然后选择「图像」标签。
3. 在「图像生成引擎」字段中，选择「ComfyUI」。
4. 在「API URL」字段中，输入 ComfyUI 的 API 访问地址，格式如下：`http://<your_comfyui_address>:8188/`。
   * 将环境变量 `COMFYUI_BASE_URL` 设置为此地址，以确保它在 WebUI 中持续有效。

#### 步骤 2：验证连接并启用图像生成

1. 确保 ComfyUI 正在运行，并且您已成功验证与 Open WebUI 的连接。没有成功连接，您将无法继续。
2. 连接验证成功后，启用「图像生成（实验性）」功能。将会显示更多选项。
3. 继续进行步骤 3 以完成最终配置。

#### 步骤 3：配置 ComfyUI 设置并导入工作流

1. 在 ComfyUI 中启用开发者模式。在 ComfyUI 中找到「队列提示」按钮上方的齿轮图标，启用「开发者模式」开关。
2. 使用「保存（API 格式）」按钮以 API 格式从 ComfyUI 导出所需的工作流。如果操作正确，文件将被下载为 `workflow_api.json`。
3. 返回 Open WebUI 并点击「点击此处上传 workflow.json 文件」按钮。
4. 选择 `workflow_api.json` 文件，将导出的工作流从 ComfyUI 导入到 Open WebUI。
5. 导入工作流后，您必须根据导入的工作流节点 ID 映射「ComfyUI 工作流节点」。
6. 将「设置默认模型」设置为正在使用的模型文件名，如 `flux1-dev.safetensors`

:::info
您可能需要在 Open WebUI 的「ComfyUI 工作流节点」部分调整一个或两个「输入键」，以匹配工作流中的节点。
例如，`seed` 可能需要重命名为 `noise_seed` 以匹配导入工作流中的节点 ID。
:::
:::tip
某些工作流，如使用任何 Flux 模型的工作流，可能会使用多个必须在 Open WebUI 中填写其节点条目字段的节点 ID。如果节点条目字段需要多个 ID，节点 ID 应该用逗号分隔（例如 `1` 或 `1, 2`）。
:::

6. 点击「保存」以应用设置，开始使用集成了 ComfyUI 的 Open WebUI 进行图像生成！

完成这些步骤后，您的 ComfyUI 设置应该已与 Open WebUI 集成，您可以使用 Flux.1 模型进行图像生成。

### 配置 SwarmUI

SwarmUI 使用 ComfyUI 作为后端。为了让 Open WebUI 与 SwarmUI 配合使用，您需要在 `ComfyUI Base URL` 后面附加 `ComfyBackendDirect`。此外，您还需要设置 SwarmUI 的局域网访问。完成上述调整后，设置 SwarmUI 与 Open WebUI 配合使用的步骤将与上面概述的[步骤一：配置 Open WebUI 设置](https://github.com/open-webui/docs/edit/main/docs/features/images.md#step-1-configure-open-webui-settings)相同。
![使用局域网访问安装 SwarmUI](https://github.com/user-attachments/assets/a6567e13-1ced-4743-8d8e-be526207f9f6)

#### SwarmUI API URL

您将输入作为 ComfyUI Base URL 的地址将如下所示：`http://<your_swarmui_address>:7801/ComfyBackendDirect`

## OpenAI DALL·E

Open WebUI 还支持通过 **OpenAI DALL·E API** 生成图像。此选项包括一个选择器，用于在 DALL·E 2 和 DALL·E 3 之间选择，每个版本都支持不同的图像尺寸。

### 初始设置

1. 从 OpenAI 获取 [API 密钥](https://platform.openai.com/api-keys)。

### 配置 Open WebUI

1. 在 Open WebUI 中，导航至「管理面板」>「设置」>「图像」菜单。
2. 将「图像生成引擎」字段设置为「Open AI (Dall-E)」。
3. 输入您的 OpenAI API 密钥。
4. 选择您想要使用的 DALL·E 模型。注意，图像尺寸选项将取决于所选模型：
   * **DALL·E 2**：支持 `256x256`、`512x512` 或 `1024x1024` 图像。
   * **DALL·E 3**：支持 `1024x1024`、`1792x1024` 或 `1024x1792` 图像。

### Azure OpenAI

直接使用 Azure OpenAI Dall-E 是不支持的，但您可以[设置 LiteLLM 代理](https://litellm.vercel.app/docs/image_generation)，它与「Open AI (Dall-E)」图像生成引擎兼容。

## 使用图像生成

![图像生成教程](/img/tutorial_image_generation.png)

1. 首先，使用文本生成模型编写图像生成提示词。
2. 响应完成后，您可以点击图片图标生成图像。
3. 图像生成完成后，它将自动在聊天中显示。

:::tip
    您也可以编辑 LLM 的响应，并输入您自己的图像生成提示词作为消息
    来进行图像生成，而不是使用 LLM 提供的实际响应。
:::
