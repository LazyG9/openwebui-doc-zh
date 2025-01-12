# Docker Compose 设置

使用 Docker Compose 可以简化多容器 Docker 应用程序的管理。

如果您还没有安装 Docker，请查看我们的 [Docker 安装教程](docs/tutorials/docker-install.md)。

Docker Compose 需要一个额外的包：`docker-compose-v2`。

**警告：** 较旧的 Docker Compose 教程可能会引用版本 1 的语法，使用类似 `docker-compose build` 的命令。请确保使用版本 2 的语法，即使用类似 `docker compose build` 的命令（注意连字符变成了空格）。

## `docker-compose.yml` 示例

以下是使用 Docker Compose 设置 Open WebUI 的示例配置文件：

```yaml
version: '3'
services:
  openwebui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3000:8080"
    volumes:
      - open-webui:/app/backend/data
volumes:
  open-webui:
```

## 启动服务

要启动您的服务，运行以下命令：

```bash
docker compose up -d
```

## 辅助脚本

代码库中包含了一个名为 `run-compose.sh` 的实用辅助脚本。这个脚本可以帮助您选择要在部署中包含的 Docker Compose 文件，简化设置过程。

---

**注意：** 要支持 Nvidia GPU，请在 `docker-compose.yml` 文件的服务定义中添加以下内容：

```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: all
          capabilities: [gpu]
```

这个设置确保您的应用程序可以在可用时利用 GPU 资源。
