## Docker 快速入门 🐳

按照以下步骤使用 Docker 安装 Open WebUI。

## 步骤 1：拉取 Open WebUI 镜像

首先从 GitHub Container Registry 拉取最新的 Open WebUI Docker 镜像。

```bash
docker pull ghcr.io/open-webui/open-webui:main
```

## 步骤 2：运行容器

使用默认设置运行容器。此命令包含卷映射以确保数据持久存储。

```bash
docker run -d -p 3000:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

### 重要标志

- **卷映射 (`-v open-webui:/app/backend/data`)**: 确保数据持久存储。这可以防止容器重启时数据丢失。
- **端口映射 (`-p 3000:8080`)**: 在本地机器的 3000 端口上暴露 WebUI。

### 使用 GPU 支持

要支持 Nvidia GPU，在 `docker run` 命令中添加 `--gpus all`：

```bash
docker run -d -p 3000:8080 --gpus all -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:cuda
```


#### 单用户模式（禁用登录）

要在单用户设置中绕过登录页面，将 `WEBUI_AUTH` 环境变量设置为 `False`：

```bash
docker run -d -p 3000:8080 -e WEBUI_AUTH=False -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

:::warning
在进行此更改后，您无法在单用户模式和多账户模式之间切换。
:::

#### 高级配置：连接到不同服务器上的 Ollama

要将 Open WebUI 连接到位于其他主机上的 Ollama 服务器，添加 `OLLAMA_BASE_URL` 环境变量：

```bash
docker run -d -p 3000:8080 -e OLLAMA_BASE_URL=https://example.com -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

## 访问 WebUI

容器运行后，在以下地址访问 Open WebUI：

[http://localhost:3000](http://localhost:3000)

有关每个 Docker 标志的详细帮助，请参阅 [Docker 文档](https://docs.docker.com/engine/reference/commandline/run/)。
