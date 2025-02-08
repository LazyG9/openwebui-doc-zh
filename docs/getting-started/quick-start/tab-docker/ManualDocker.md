## Docker 快速部署 🐳

按照以下步骤使用 Docker 安装 Open WebUI。

## 步骤 1：Pull Open WebUI 镜像

首先从 GitHub Container Registry 拉取最新的 Open WebUI Docker 镜像。

```bash
docker pull ghcr.io/open-webui/open-webui:main
```

## 步骤 2：启动容器

使用默认设置启动容器。此命令包含数据卷映射以确保数据持久化存储。

```bash
docker run -d -p 3000:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

### 重要配置参数

- **数据卷映射 (Volume Mapping: `-v open-webui:/app/backend/data`)**：确保数据持久化存储，防止容器重启时数据丢失。
- **端口映射 (Port Mapping: `-p 3000:8080`)**：将 WebUI 暴露在本地机器的 3000 端口上。

### 使用 GPU 支持

要启用 Nvidia GPU 支持，在 `docker run` 命令中添加 `--gpus all`：

```bash
docker run -d -p 3000:8080 --gpus all -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:cuda
```


#### 单用户部署模式（禁用登录）

要在单用户部署中绕过登录页面，将 `WEBUI_AUTH` 环境变量设置为 `False`：

```bash
docker run -d -p 3000:8080 -e WEBUI_AUTH=False -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
```

:::warning
在进行此配置后，您将无法在单用户模式和多账户模式之间切换。
:::

#### 高级部署配置：连接到其他服务器上的 Ollama

要将 Open WebUI 连接到位于其他主机上的 Ollama 服务器，添加 `OLLAMA_BASE_URL` 环境变量：

```bash
docker run -d -p 3000:8080 -e OLLAMA_BASE_URL=https://example.com -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

## 访问界面

容器启动后，在以下地址访问 Open WebUI：

[http://localhost:3000](http://localhost:3000)

有关各项 Docker 参数的详细说明，请参阅 [Docker 官方文档](https://docs.docker.com/engine/reference/commandline/run/)。
