# Docker Compose 配置

使用 Docker Compose 可以简化多容器 Docker 应用的管理。

如果您尚未安装 Docker，请查看我们的 [Docker 安装教程](docs/tutorials/docker-install.md)。

Docker Compose 需要安装额外的包 `docker-compose-v2`。

**注意事项：** 较旧的 Docker Compose 教程可能引用版本 1 的语法，使用类似 `docker-compose build` 的命令。请确保使用版本 2 的语法，使用类似 `docker compose build` 的命令（注意空格而不是连字符）。

## `docker-compose.yml` 配置文件示例

以下是使用 Docker Compose 配置 Open WebUI 的示例文件：

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

## 启动应用

要启动您的应用，运行以下命令：

```bash
docker compose up -d
```

## 便捷脚本

代码库中包含了一个名为 `run-compose.sh` 的实用便捷脚本。此脚本可以帮助您选择要在部署中包含的 Docker Compose 文件，简化配置流程。

---

**注意：** 要启用 Nvidia GPU 支持，请在 `docker-compose.yml` 文件的服务定义中添加以下内容：

```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: all
          capabilities: [gpu]
```

此配置确保您的应用可以在可用时利用 GPU 资源。
