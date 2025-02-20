# Docker Compose 部署指南

Docker Compose 是一个强大的工具，可以帮助您轻松管理多容器化的 Docker 应用程序。

如果您还没有安装 Docker，请参考我们的 [Docker 安装教程](docs/tutorials/docker-install.md)。

使用 Docker Compose 需要额外安装 `docker-compose-v2` 包。

**重要提示：** 请注意，Docker Compose 有两个主要版本。旧版本（V1）使用 `docker-compose build` 这样的命令格式，而新版本（V2）使用 `docker compose build` 格式（注意空格代替了连字符）。请确保使用 V2 版本的命令格式。

## Docker Compose 配置示例

以下是一个基础的 Open WebUI Docker Compose 配置文件示例：

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

使用以下命令启动所有服务：

```bash
docker compose up -d
```

## 部署辅助工具

项目提供了一个名为 `run-compose.sh` 的实用脚本工具。该脚本可以帮助您根据实际需求选择和组合不同的 Docker Compose 配置文件，使部署过程更加灵活和便捷。

---

**GPU 支持说明：** 如果您需要启用 Nvidia GPU 支持，需要进行以下修改：
1. 将镜像更改为 `ghcr.io/open-webui/open-webui:cuda`
2. 在服务配置中添加以下 GPU 设备配置：

```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: all
          capabilities: [gpu]
```

这些配置将确保应用程序能够正确识别和使用系统中的 Nvidia GPU 资源。
