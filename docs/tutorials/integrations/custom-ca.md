---
sidebar_position: 14
title: "🛃 使用自定义 CA 存储进行设置"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何为您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

如果您在尝试运行 OI 时遇到 `[SSL: CERTIFICATE_VERIFY_FAILED]` 错误，很可能是因为您在一个拦截 HTTPS 流量的网络中（例如企业网络）。

要解决这个问题，您需要将新的证书添加到 OI 的信任存储中。

**对于预构建的 Docker 镜像**：

1. 通过向 `docker run` 传递 `--volume=/etc/ssl/certs/ca-certificiate.crt:/etc/ssl/certs/ca-certificiates.crt:ro` 命令行选项，将证书存储从主机挂载到容器中
2. 通过设置 `REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` 强制 Python 使用系统信任存储（参见 https://docs.docker.com/reference/cli/docker/container/run/#env）

如果环境变量 `REQUESTS_CA_BUNDLE` 不起作用，请尝试设置 `SSL_CERT_FILE`（根据 [httpx 文档](https://www.python-httpx.org/environment_variables/#ssl_cert_file)）并使用相同的值。

来自 [@KizzyCode](https://github.com/open-webui/open-webui/issues/1398#issuecomment-2258463210) 的 `compose.yaml` 示例：

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

`ro` 标志将 CA 存储挂载为只读，防止意外更改主机 CA 存储。

**对于本地开发**：

您也可以通过修改 `Dockerfile` 在构建过程中添加证书。例如，如果您想对 UI 进行更改，这会很有用。
由于构建发生在[多个阶段](https://docs.docker.com/build/building/multi-stage/)，您必须在两个阶段都添加证书：

1. 前端（`build` 阶段）：

```dockerfile
COPY package.json package-lock.json <YourRootCert>.crt ./
ENV NODE_EXTRA_CA_CERTS=/app/<YourRootCert>.crt
RUN npm ci
```

2. 后端（`base` 阶段）：

```dockerfile
COPY <CorporateSSL.crt> /usr/local/share/ca-certificates/
RUN update-ca-certificates
ENV PIP_CERT=/etc/ssl/certs/ca-certificates.crt \
    REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt
```
