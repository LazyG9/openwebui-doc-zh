---
sidebar_position: 30
title: "🔗 Redis WebSocket 支持"
---

:::warning
注意：本教程由社区成员贡献，并非 OpenWebUI 官方团队支持的内容。它仅用于演示如何根据特定需求自定义 OpenWebUI。如果您也想贡献内容，请参考贡献指南教程。
:::

# 🔗 Redis WebSocket 支持

## 概述

本文档详细说明了如何将 Redis 与 Open WebUI 集成以支持 WebSocket 功能的步骤。按照本指南操作，您将能够在 Open WebUI 实例中启用 WebSocket 功能，从而实现客户端与应用程序之间的实时通信和数据更新。

### 前提条件

* 正常运行的 Open WebUI 实例（版本 1.0 或更高）
* Redis 容器（本教程使用 `docker.io/valkey/valkey:8.0.1-alpine`，该镜像基于最新的 Redis 7.x 版本）
* 已安装 Docker Compose（版本 2.0 或更高）
* 用于 Open WebUI 和 Redis 通信的 Docker 网络
* 基本掌握 Docker、Redis 和 Open WebUI 的使用方法

## 设置 Redis

要配置支持 WebSocket 的 Redis 环境，请创建一个 `docker-compose.yml` 文件，内容如下：

```yml
version: '3.9'
services:
  redis:
    image: docker.io/valkey/valkey:8.0.1-alpine
    container_name: redis-valkey
    volumes:
      - redis-data:/data    # 数据持久化存储
    command: "valkey-server --save 30 1"    # 每30分钟保存数据
    healthcheck:    # 健康检查配置
      test: "[ $$(valkey-cli ping) = 'PONG' ]"
      start_period: 5s
      interval: 1s
      timeout: 3s
      retries: 5
    restart: unless-stopped    # 自动重启策略
    cap_drop:    # 安全限制
      - ALL
    cap_add:
      - SETGID
      - SETUID
      - DAC_OVERRIDE
    logging:    # 日志配置
      driver: "json-file"
      options:
        max-size: "1m"
        max-file: "1"
    networks:    # 网络配置
      - openwebui-network

volumes:
  redis-data:    # 数据卷定义

networks:
  openwebui-network:
    external: true    # 使用外部网络
```

:::info 重要说明

1. 配置中未包含 `ports` 配置项，因为通常不需要。Redis 服务可以通过 Docker 网络直接被 Open WebUI 服务访问。
2. 如果需要从 Docker 网络外部访问 Redis（例如调试或监控），可以添加 `ports: - "6379:6379"` 配置。
3. 此配置创建了名为 `redis-valkey` 的 Redis 容器，并配置了数据持久化。
4. `healthcheck` 配置确保服务健康状态，当无法响应 `ping` 命令时自动重启。
5. `--save 30 1` 参数表示：当至少有 1 个键变更时，每 30 分钟自动保存数据。

:::

创建 Open WebUI 和 Redis 通信用的 Docker 网络，执行：

```bash
docker network create openwebui-network
```

## 配置 Open WebUI

在 Open WebUI 中启用 WebSocket 支持，需要设置以下环境变量：

```bash
# 启用 WebSocket 支持
ENABLE_WEBSOCKET_SUPPORT="true"
# 指定 Redis 作为 WebSocket 管理器
WEBSOCKET_MANAGER="redis"
# Redis 连接地址
WEBSOCKET_REDIS_URL="redis://redis:6379/1"
```

使用 Docker 运行 Open WebUI 时，确保连接到同一个 Docker 网络：

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

注意：请将 `127.0.0.1` 替换为您 Docker 网络中 Redis 容器的实际 IP 地址。

## 验证安装

正确配置后，启动 Open WebUI 实例时应该能看到以下日志信息：

`DEBUG:open_webui.socket.main:Using Redis to manage websockets.`

这表明 Open WebUI 已成功配置使用 Redis 管理 WebSocket 连接。

您可以使用以下命令验证 Redis 服务是否正常运行：

```bash
# 方法一：使用 redis-cli
docker exec -it redis-valkey redis-cli -p 6379 ping

# 如果上述命令失败，可以尝试使用 valkey-cli
docker exec -it redis-valkey valkey-cli -p 6379 ping
```

正常情况下，命令应返回 `PONG`。

## 故障排除指南

如果遇到 Redis 或 WebSocket 相关问题，可参考以下资源：

* [Redis 官方文档](https://redis.io/docs)（包含完整的 Redis 配置和使用说明）
* [Docker Compose 官方文档](https://docs.docker.com/compose/overview/)（容器编排相关问题）
* [sysctl 系统配置文档](https://man7.org/linux/man-pages/man8/sysctl.8.html)（系统级配置问题）

按照本指南的步骤和故障排除建议，您应该能够成功配置 Redis 和 Open WebUI 的 WebSocket 支持，实现应用程序的实时通信功能。
