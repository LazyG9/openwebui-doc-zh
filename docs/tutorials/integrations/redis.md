---
sidebar_position: 30
title: "🔗 Redis WebSocket 支持"
---

:::warning
本教程由社区贡献，并非 Open WebUI 团队官方支持的内容。它仅作为如何根据特定需求自定义 Open WebUI 的参考示例。如果您想要贡献内容，请参阅贡献指南。
:::

# 🔗 Redis WebSocket 支持

## 概述

本文档详细说明了如何将 Redis 与 Open WebUI 集成以实现 WebSocket 支持的具体步骤。按照本指南操作，您将能够在 Open WebUI 实例中启用 WebSocket 功能，从而实现客户端和应用程序之间的实时通信与数据更新。

### 前提条件

* 运行版本 1.0 或更高版本的 Open WebUI 实例
* Redis 容器（本教程使用基于最新 Redis 7.x 版本的 `docker.io/valkey/valkey:8.0.1-alpine`）
* 系统已安装 Docker Compose（版本 2.0 或更高）
* 用于 Open WebUI 和 Redis 通信的 Docker 网络
* 具备 Docker、Redis 和 Open WebUI 的基础知识

## 设置 Redis

要配置支持 WebSocket 的 Redis，请创建一个 `docker-compose.yml` 文件并输入以下内容：

```yml
version: '3.9'
services:
  redis:
    image: docker.io/valkey/valkey:8.0.1-alpine
    container_name: redis-valkey
    volumes:
      - redis-data:/data
    command: "valkey-server --save 30 1"
    healthcheck:
      test: "[ $$(valkey-cli ping) = 'PONG' ]"
      start_period: 5s
      interval: 1s
      timeout: 3s
      retries: 5
    restart: unless-stopped
    cap_drop:
      - ALL
    cap_add:
      - SETGID
      - SETUID
      - DAC_OVERRIDE
    logging:
      driver: "json-file"
      options:
        max-size: "1m"
        max-file: "1"
    networks:
      - openwebui-network

volumes:
  redis-data:

networks:
  openwebui-network:
    external: true
```

:::info 说明
该配置默认不包含 `ports` 指令，因为在大多数情况下并不需要。Open WebUI 服务可以直接通过 Docker 网络访问 Redis 服务。如果您需要从 Docker 网络外部访问 Redis 实例（例如进行调试或监控），可以添加 `ports` 指令来开放 Redis 端口（如 `6379:6379`）。

上述配置会创建一个名为 `redis-valkey` 的 Redis 容器，并挂载数据卷以实现数据持久化。其中，`healthcheck` 指令确保容器在无法响应 `ping` 命令时自动重启。通过 `--save 30 1` 参数设置，当至少有 1 个键发生变化时，系统会每 30 分钟将 Redis 数据库保存到磁盘一次。
:::

执行以下命令来创建 Open WebUI 和 Redis 之间的通信所需的 Docker 网络：

```bash
docker network create openwebui-network
```

## 配置 Open WebUI

要在 Open WebUI 中启用 WebSocket 支持，需要为您的 Open WebUI 实例配置以下环境变量：

```bash
ENABLE_WEBSOCKET_SUPPORT="true"
WEBSOCKET_MANAGER="redis"
WEBSOCKET_REDIS_URL="redis://redis:6379/1"
```

这些环境变量分别用于：启用 WebSocket 支持、指定 Redis 作为 WebSocket 管理器，以及设置 Redis URL。请注意将 `WEBSOCKET_REDIS_URL` 的值替换为您实际 Redis 实例的 IP 地址。

使用 Docker 运行 Open WebUI 时，需要将其连接到之前创建的 Docker 网络：

```bash
docker run -d \
  --name open-webui \
  --network openwebui-network \
  -v open-webui:/app/backend/data \
  -e ENABLE_WEBSOCKET_SUPPORT="true" \
  -e WEBSOCKET_MANAGER="redis" \
  -e WEBSOCKET_REDIS_URL="redis://127.0.0.1:6379/1" \
  ghcr.io/open-webui/open-webui:main
```

注意：请将命令中的 `127.0.0.1` 替换为您 Docker 网络中 Redis 容器的实际 IP 地址。

## 验证部署

如果您已正确完成 Redis 设置和 Open WebUI 配置，启动 Open WebUI 实例时应该能看到以下日志信息：

`DEBUG:open_webui.socket.main:Using Redis to manage websockets.`

这表明 Open WebUI 已成功使用 Redis 进行 WebSocket 管理。您还可以通过 `docker exec` 命令验证 Redis 实例的运行状态：

```bash
docker exec -it redis-valkey redis-cli -p 6379 ping
```

如果 Redis 运行正常，该命令将返回 `PONG`。如果上述命令失败，可以尝试使用以下替代命令：

```bash
docker exec -it redis-valkey valkey-cli -p 6379 ping
```

## 故障排除

如果您在使用 Open WebUI 的 Redis 或 WebSocket 功能时遇到问题，可以参考以下资源：

* [Redis 官方文档](https://redis.io/docs)
* [Docker Compose 官方文档](https://docs.docker.com/compose/overview/)
* [sysctl 系统文档](https://man7.org/linux/man-pages/man8/sysctl.8.html)

按照本指南的步骤和故障排除建议，您应该能够成功配置 Redis 与 Open WebUI 的 WebSocket 支持，实现客户端和应用程序之间的实时通信功能。
