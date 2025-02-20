---
sidebar_position: 14
title: "🛃 配置自定义证书存储"
---

:::warning
本教程来自社区贡献，未经 Open WebUI 团队官方支持。它仅用于演示如何根据特定需求自定义 Open WebUI。如果您想要贡献内容，请参考贡献指南教程。
:::

如果您在运行 OI 时遇到 `[SSL: CERTIFICATE_VERIFY_FAILED]` 错误，很可能是因为您所在的网络（比如企业网络）会拦截 HTTPS 流量。

要解决这个问题，您需要将新的证书添加到 OI 的证书存储中。

**针对预构建的 Docker 镜像**：

1. 通过在 `docker run` 命令中添加 `--volume=/etc/ssl/certs/ca-certificates.crt:/etc/ssl/certs/ca-certificates.crt:ro` 参数，将主机的证书存储挂载到容器中
2. 通过设置环境变量 `REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` 来强制 Python 使用系统证书存储（详见 https://docs.docker.com/reference/cli/docker/container/run/#env）

如果环境变量 `REQUESTS_CA_BUNDLE` 不生效，可以尝试设置 `SSL_CERT_FILE`（参考 [httpx 文档](https://www.python-httpx.org/environment_variables/#ssl_cert_file)）并使用相同的路径值。

以下是 [@KizzyCode](https://github.com/open-webui/open-webui/issues/1398#issuecomment-2258463210) 提供的 `compose.yaml` 示例：

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

`ro` 标志表示以只读方式挂载证书存储，这可以防止对主机证书存储进行意外修改。

**针对本地开发环境**：

您也可以通过修改 `Dockerfile` 在构建过程中添加证书。这对于需要修改 UI 等场景特别有用。
由于构建过程包含[多个阶段](https://docs.docker.com/build/building/multi-stage/)，您需要在以下两个阶段都添加证书：

1. 前端构建阶段（`build` 阶段）：

```dockerfile
COPY package.json package-lock.json <YourRootCert>.crt ./
ENV NODE_EXTRA_CA_CERTS=/app/<YourRootCert>.crt
RUN npm ci
```

2. 后端基础阶段（`base` 阶段）：

```dockerfile
COPY <CorporateSSL.crt> /usr/local/share/ca-certificates/
RUN update-ca-certificates
ENV PIP_CERT=/etc/ssl/certs/ca-certificates.crt \
    REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```
