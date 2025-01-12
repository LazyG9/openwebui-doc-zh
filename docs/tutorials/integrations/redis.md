---
sidebar_position: 30
title: "🔗 Redis Websocket 支持"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 🔗 Redis Websocket 支持

## 概述

本文档页面概述了将 Redis 与 Open WebUI 集成以支持 websocket 所需的步骤。通过按照这些步骤操作，您将能够在 Open WebUI 实例中启用 websocket 功能，从而实现客户端和应用程序之间的实时通信和更新。

### 先决条件

* 有效的 Open WebUI 实例（运行版本 1.0 或更高）
* Redis 容器（在本例中，我们将使用 `docker.io/valkey/valkey:8.0.1-alpine`，它基于最新的 Redis 7.x 版本）
* 系统上安装了 Docker Composer（版本 2.0 或更高）
* 用于 Open WebUI 和 Redis 之间通信的 Docker 网络
* 对 Docker、Redis 和 Open WebUI 的基本了解

## 设置 Redis

要设置 Redis 以支持 websocket，您需要创建一个包含以下内容的 `docker-compose.yml` 文件：

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

:::info 注意

此配置中未包含 `ports` 指令，因为在大多数情况下不需要。Redis 服务仍然可以通过 Docker 网络被 Open WebUI 服务访问。但是，如果您需要从 Docker 网络外部访问 Redis 实例（例如，用于调试或监控目的），您可以添加 `ports` 指令来暴露 Redis 端口（例如，`6379:6379`）。

上述配置设置了一个名为 `redis-valkey` 的 Redis 容器，并挂载了一个用于数据持久化的卷。`healthcheck` 指令确保容器在无法响应 `ping` 命令时重启。`--save 30 1` 命令选项在至少有 1 个键发生更改时每 30 分钟将 Redis 数据库保存到磁盘。

:::

要创建用于 Open WebUI 和 Redis 之间通信的 Docker 网络，请运行以下命令：

```bash
docker network create openwebui-network
```

## 配置 Open WebUI

要启用 Open WebUI 中的 websocket 支持，您需要设置以下环境变量：

```bash
ENABLE_WEBSOCKET_SUPPORT="true"
WEBSOCKET_MANAGER="redis"
WEBSOCKET_REDIS_URL="redis://redis:6379/1"
```

这些环境变量启用 websocket 支持，指定 Redis 作为 websocket 管理器，并定义 Redis URL。请确保将 `WEBSOCKET_REDIS_URL` 值替换为实际的 Redis 实例 IP 地址。

当使用 Docker 运行 Open WebUI 时，您需要将其连接到相同的 Docker 网络：

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

将 `127.0.0.1` 替换为实际的 Redis 容器 IP 地址。

## 验证

如果正确设置了 Redis 并配置了 Open WebUI，您应该在启动 Open WebUI 实例时看到以下日志消息：

`DEBUG:open_webui.socket.main:Using Redis to manage websockets.`

这确认了 Open WebUI 正在使用 Redis 进行 websocket 管理。您还可以使用 `docker exec` 命令来验证 Redis 实例是否正在运行并接受连接：

```bash
docker exec -it redis-valkey redis-cli -p 6379 ping
```

此命令应输出 `PONG` 如果 Redis 实例运行正常。如果此命令失败，您可以尝试此命令：

```bash
docker exec -it redis-valkey valkey-cli -p 6379 ping
```

## 故障排除

如果您遇到与 Redis 或 websocket 支持相关的 Open WebUI 问题，您可以参考以下资源进行故障排除：

* [Redis 文档](https://redis.io/docs)
* [Docker Compose 文档](https://docs.docker.com/compose/overview/)
* [sysctl 文档](https://man7.org/linux/man-pages/man8/sysctl.8.html)

通过按照这些步骤并遵循故障排除提示，您应该能够设置 Redis 与 Open WebUI 的 websocket 支持，并启用客户端和应用程序之间的实时通信和更新。
