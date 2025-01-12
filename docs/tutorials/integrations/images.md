---
sidebar_position: 6
title: "🎨 图像生成"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 🎨 图像生成

Open WebUI 支持通过三个后端进行图像生成：**AUTOMATIC1111**、**ComfyUI** 和 **OpenAI DALL·E**。本指南将帮助您设置和使用这些选项。

## AUTOMATIC1111

Open WebUI 支持通过 **AUTOMATIC1111** [API](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API) 生成图像。以下是开始使用的步骤：

### 初始设置

1. 确保您已安装 [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)。
2. 使用额外的标志启动 AUTOMATIC1111 以启用 API 访问：

   ```
   ./webui.sh --api --listen
   ```

3. 对于预设环境变量的 WebUI Docker 安装，使用以下命令：

   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e AUTOMATIC1111_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 使用 AUTOMATIC1111 设置 Open WebUI

1. 在 Open WebUI 中，导航到 **Admin Panel** > **Settings** > **Images** 菜单。
2. 将 `Image Generation Engine` 字段设置为 `Default (Automatic1111)`。
3. 在 API URL 字段中，输入可访问 AUTOMATIC1111 API 的地址：

   ```
   http://<your_automatic1111_address>:7860/
   ```

   如果您在同一主机上运行 Open WebUI 和 AUTOMATIC1111 的 Docker 安装，请使用 `http://host.docker.internal:7860/` 作为您的地址。

## ComfyUI

ComfyUI 提供了另一种管理图像生成模型的接口。更多信息或从其 [GitHub 页面](https://github.com/comfyanonymous/ComfyUI) 下载。以下是设置 ComfyUI 以与您的其他工具一起运行的步骤。

### 初始设置

1. 从 [GitHub](https://github.com/comfyanonymous/ComfyUI) 下载并提取 ComfyUI 软件包到您想要的目录。
2. 要启动 ComfyUI，请运行以下命令：

   ```
   python main.py
   ```

   对于 VRAM 较低的系统，使用额外的标志启动 ComfyUI 以减少内存使用：

   ```
   python main.py --lowvram
   ```

3. 对于预设环境变量的 WebUI Docker 安装，使用以下命令：

   ```
   docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -e COMFYUI_BASE_URL=http://host.docker.internal:7860/ -e ENABLE_IMAGE_GENERATION=True -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
   ```

### 使用 ComfyUI 设置 Open WebUI

#### 设置 FLUX.1 模型

1. **模型检查点**：

* 从 [black-forest-labs HuggingFace 页面](https://huggingface.co/black-forest-labs) 下载 `FLUX.1-schnell` 或 `FLUX.1-dev` 模型。
* 将模型检查点（s）放置在 ComfyUI 的 `models/checkpoints` 和 `models/unet` 目录中。或者，您可以在 `models/checkpoints` 和 `models/unet` 之间创建一个符号链接，以确保两个目录都包含相同的模型检查点。

2. **VAE 模型**：

* 从 [这里](https://huggingface.co/black-forest-labs/FLUX.1-schnell/blob/main/ae.safetensors) 下载 `ae.safetensors` VAE。
* 将其放置在 ComfyUI 的 `models/vae` 目录中。

3. **CLIP 模型**：

* 从 [这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main) 下载 `clip_l.safetensors`。
* 将其放置在 ComfyUI 的 `models/clip` 目录中。

4. **T5XXL 模型**：

* 从 [这里](https://huggingface.co/comfyanonymous/flux_text_encoders/tree/main) 下载 `t5xxl_fp16.safetensors` 或 `t5xxl_fp8_e4m3fn.safetensors` 模型。
* 将其放置在 ComfyUI 的 `models/clip` 目录中。

要集成 ComfyUI 到 Open WebUI，请执行以下步骤：

#### 步骤 1：配置 Open WebUI 设置

1. 导航到 Open WebUI 中的 **Admin Panel**。
2. 点击 **Settings** 并选择 **Images** 选项卡。
3. 在 `Image Generation Engine` 字段中选择 `ComfyUI`。
4. 在 **API URL** 字段中输入可访问 ComfyUI API 的地址，格式如下：`http://<your_comfyui_address>:8188/`。
   * 将环境变量 `COMFYUI_BASE_URL` 设置为此地址，以确保它持久存在于 WebUI 中。

#### 步骤 2：验证连接并启用图像生成

1. 确保 ComfyUI 正在运行，并且您已成功验证与 Open WebUI 的连接。您将无法继续，除非连接成功。
2. 连接成功后，切换 **Image Generation (Experimental)**。将显示更多选项。
3. 继续进行步骤 3 以完成最终配置步骤。

#### 步骤 3：配置 ComfyUI 设置和导入工作流

1. 在 ComfyUI 中启用开发者模式。为此，请在 ComfyUI 中查找齿轮图标并启用 `Dev Mode` 切换。
2. 使用 `Save (API Format)` 按钮将所需的工作流从 ComfyUI 导出为 `API 格式`。文件将下载为 `workflow_api.json` 如果正确完成。
3. 返回 Open WebUI 并点击 **Click here to upload a workflow.json file** 按钮。
4. 选择 `workflow_api.json` 文件以将导出的工作流从 ComfyUI 导入到 Open WebUI。
5. 导入工作流后，您必须映射 `ComfyUI Workflow Nodes` 根据导入的工作流节点 ID。
6. 将 `Set Default Model` 设置为正在使用的模型文件的名称，例如 `flux1-dev.safetensors`

:::info
您可能需要调整 Open WebUI 的 `ComfyUI Workflow Nodes` 部分中的一个或两个 `Input Key` 以匹配工作流中的节点。
例如，`seed` 可能需要重命名为 `noise_seed` 以匹配工作流中的节点 ID。
:::
:::tip
一些工作流（例如使用任何 Flux 模型的工作流）可能利用多个节点 ID，这些 ID 是必要的，以填充 Open WebUI 中工作流节点条目字段中的节点 ID。如果节点条目字段需要多个 ID，则节点 ID 应该是逗号分隔的（例如 `1` 或 `1, 2`）。
:::

6. 点击 `Save` 应用设置并享受使用 ComfyUI 集成到 Open WebUI 的图像生成！

完成这些步骤后，您的 ComfyUI 设置应与 Open WebUI 集成，您可以使用 Flux.1 模型进行图像生成。

### 配置 SwarmUI

SwarmUI 使用 ComfyUI 作为其后端。为了使 Open WebUI 与 SwarmUI 一起工作，您必须将 `ComfyBackendDirect` 附加到 `ComfyUI Base URL`。此外，您还需要设置 SwarmUI 与 LAN 访问。在上述调整之后，设置 SwarmUI 与 Open WebUI 一起工作将与 [步骤 1：配置 Open WebUI 设置](https://github.com/open-webui/docs/edit/main/docs/features/images.md#step-1-configure-open-webui-settings) 相同。
![Install SwarmUI with LAN Access](https://github.com/user-attachments/assets/a6567e13-1ced-4743-8d8e-be526207f9f6)

#### SwarmUI API URL

您将输入的 ComfyUI Base URL 将如下所示：`http://<your_swarmui_address>:7801/ComfyBackendDirect`

## OpenAI DALL·E

Open WebUI 还支持通过 **OpenAI DALL·E APIs** 生成图像。此选项包括选择 DALL·E 2 和 DALL·E 3 的选项，每个选项支持不同的图像大小。

### 初始设置

1. 从 OpenAI 获取 [API 密钥](https://platform.openai.com/api-keys)。

### 配置 Open WebUI

1. 在 Open WebUI 中，导航到 **Admin Panel** > **Settings** > **Images** 菜单。
2. 将 `Image Generation Engine` 字段设置为 `Open AI (Dall-E)`。
3. 输入您的 OpenAI API 密钥。
4. 选择您要使用的 DALL·E 模型。请注意，图像大小选项将取决于所选模型：
   * **DALL·E 2**：支持 `256x256`、`512x512` 或 `1024x1024` 图像。
   * **DALL·E 3**：支持 `1024x1024`、`1792x1024` 或 `1024x1792` 图像。

### Azure OpenAI

使用 Azure OpenAI Dall-E 直接是不支持的，但您可以 [设置 LiteLLM 代理](https://litellm.vercel.app/docs/image_generation)，它与 `Open AI (Dall-E)` 图像生成引擎兼容。

## 使用图像生成

![Image Generation Tutorial](/img/tutorial_image_generation.png)

1. 首先，使用文本生成模型编写图像生成提示。
2. 响应完成后，您可以点击图片图标生成图像。
3. 图像生成完成后，它将自动返回聊天。

:::tip
   您还可以编辑 LLM 的响应并输入图像生成提示作为消息
   发送给图像生成而不是使用实际响应提供的
   由 LLM 提供。
:::
