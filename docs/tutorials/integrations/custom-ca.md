---
sidebar_position: 14
title: "🛃 配置自定义 SSL 证书存储"
---

:::warning
注意：本教程来自社区贡献，不由 Open WebUI 团队官方支持。它仅用于演示如何根据具体需求自定义 Open WebUI。如果您也想为社区做出贡献，欢迎参考贡献指南。
:::

如果您在运行 Open WebUI 时遇到 `[SSL: CERTIFICATE_VERIFY_FAILED]` 证书验证失败错误，这很可能是因为您所在的网络环境（例如企业内网）会对 HTTPS 流量进行拦截和检查。

要解决这个问题，您需要将网络环境的 SSL 证书添加到 Open WebUI 的证书信任存储中。

**使用预构建 Docker 镜像的配置方法**：

1. 在运行容器时，通过添加以下卷挂载选项，将主机上的证书存储挂载到容器中：
   ```bash
   --volume=/etc/ssl/certs/ca-certificiate.crt:/etc/ssl/certs/ca-certificiates.crt:ro
   ```

2. 设置环境变量 `REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`，强制 Python 使用系统证书存储
   （详见 https://docs.docker.com/reference/cli/docker/container/run/#env）

如果环境变量 `REQUESTS_CA_BUNDLE` 设置后仍然无效，可以尝试设置 `SSL_CERT_FILE` 环境变量（参考 [httpx 官方文档](https://www.python-httpx.org/environment_variables/#ssl_cert_file)），值保持相同。

以下是一个完整的 `compose.yaml` 配置示例（由 [@KizzyCode](https://github.com/open-webui/open-webui/issues/1398#issuecomment-2258463210) 提供）：

```yaml
services:
  openwebui:
    image: ghcr.io/open-webui/open-webui:main
    volumes:
      - /var/containers/openwebui:/app/backend/data:rw
      - /etc/containers/openwebui/compusrv.crt:/etc/ssl/certs/ca-certificates.crt:ro
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    environment:
      - WEBUI_NAME=compusrv
      - ENABLE_SIGNUP=False
      - ENABLE_COMMUNITY_SHARING=False
      - WEBUI_SESSION_COOKIE_SAME_SITE=strict
      - WEBUI_SESSION_COOKIE_SECURE=True
      - ENABLE_OLLAMA_API=False
      - REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```

注意：配置中的 `ro` 标志表示以只读方式挂载 CA 证书存储，这可以防止容器意外修改主机系统的证书存储。

**本地开发环境的配置方法**：

如果您是在本地开发环境中（例如需要修改 UI 时），您可以通过修改 `Dockerfile` 在镜像构建过程中添加证书。
由于 Docker 构建使用了[多阶段构建](https://docs.docker.com/build/building/multi-stage/)，您需要在以下两个阶段都添加证书：

1. 前端构建阶段（`build` stage）：

```dockerfile
COPY package.json package-lock.json <您的根证书>.crt ./
ENV NODE_EXTRA_CA_CERTS=/app/<您的根证书>.crt
RUN npm ci
```

2. 后端构建阶段（`base` stage）：

```dockerfile
COPY <您的SSL证书>.crt /usr/local/share/ca-certificates/
RUN update-ca-certificates
ENV PIP_CERT=/etc/ssl/certs/ca-certificates.crt \
    REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```
