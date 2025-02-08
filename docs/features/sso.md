---
sidebar_position: 19
title: "🔒 SSO：身份验证支持"
---

# SSO 身份验证支持

Open WebUI 支持多种形式的身份验证：

1. OAuth2
    1. Google 验证
    1. Microsoft 验证
    1. OIDC 标准验证
1. 可信请求头验证

## OAuth 验证

OAuth 系统提供以下全局配置选项：

1. `ENABLE_OAUTH_SIGNUP` - 设置为 `true` 时，允许用户通过 OAuth 登录时创建新账号。此选项独立于 `ENABLE_SIGNUP`。
1. `OAUTH_MERGE_ACCOUNTS_BY_EMAIL` - 允许用户登录到与 OAuth 提供商提供的邮箱地址匹配的账号。
    - 注意：此选项存在安全风险，因为并非所有 OAuth 提供商都会验证邮箱地址，可能导致账号被劫持。

### Google 验证配置

要配置 Google OAuth 客户端，请参考 [Google 官方文档](https://support.google.com/cloud/answer/6158849) 了解如何为**网络应用程序**创建 OAuth 客户端。
允许的重定向 URI 必须包含 `<open-webui>/oauth/google/callback`。

需要配置以下环境变量：

1. `GOOGLE_CLIENT_ID` - Google OAuth 客户端 ID
1. `GOOGLE_CLIENT_SECRET` - Google OAuth 客户端密钥

### Microsoft 验证配置

要配置 Microsoft OAuth 客户端，请参考 [Microsoft 官方文档](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app) 了解如何为**网络应用程序**创建 OAuth 客户端。
允许的重定向 URI 必须包含 `<open-webui>/oauth/microsoft/callback`。

目前 Microsoft OAuth 仅支持单一租户，即单个 Entra 组织或个人 Microsoft 账号。

需要配置以下环境变量：

1. `MICROSOFT_CLIENT_ID` - Microsoft OAuth 客户端 ID
1. `MICROSOFT_CLIENT_SECRET` - Microsoft OAuth 客户端密钥
1. `MICROSOFT_CLIENT_TENANT_ID` - Microsoft 租户 ID - 个人账号使用 `9188040d-6c67-4c5b-b112-36a304b66dad`

### OIDC 标准验证配置

可以配置任何支持 OIDC 标准的验证提供商。
必须提供 `email` 声明。
如果可用，系统会使用 `name` 和 `picture` 声明。
允许的重定向 URI 必须包含 `<open-webui>/oauth/oidc/callback`。

需要配置以下环境变量：

1. `OAUTH_CLIENT_ID` - OIDC 客户端 ID
1. `OAUTH_CLIENT_SECRET` - OIDC 客户端密钥
1. `OPENID_PROVIDER_URL` - OIDC 配置地址，例如 `https://accounts.google.com/.well-known/openid-configuration`
1. `OAUTH_PROVIDER_NAME` - 在界面上显示的提供商名称，默认为 SSO
1. `OAUTH_SCOPES` - 请求的权限范围，默认为 `openid email profile`

### OAuth 角色管理

任何能在访问令牌中返回角色信息的 OAuth 提供商都可以用于管理 Open WebUI 中的角色。
要使用此功能，请将 `ENABLE_OAUTH_ROLE_MANAGEMENT` 设置为 `true`。
您可以配置以下环境变量来匹配 OAuth 提供商返回的角色：

1. `OAUTH_ROLES_CLAIM` - 包含角色的声明，默认为 `roles`。也可以是嵌套的，例如 `user.roles`。
1. `OAUTH_ALLOWED_ROLES` - 允许登录的角色列表，接收 open webui 角色 `user`。
1. `OAUTH_ADMIN_ROLES` - 允许登录为管理员的角色列表，接收 open webui 角色 `admin`。

:::info

如果更改已登录用户的角色，他们需要注销并重新登录以接收新角色。

:::

## 可信请求头

Open WebUI 能够将身份验证委托给通过 HTTP 头传递用户详细信息的验证反向代理。
此页面提供了几种示例配置。

:::danger

错误的配置可能会允许用户作为您的 Open WebUI 实例上的任何用户进行身份验证。
确保仅允许验证反向代理访问 Open WebUI，例如通过设置 `HOST=127.0.0.1` 仅监听回环接口。

:::

### 通用配置

当 `WEBUI_AUTH_TRUSTED_EMAIL_HEADER` 环境变量设置时，Open WebUI 将使用指定的头值作为用户的电子邮件地址，处理自动注册和登录。

例如，设置 `WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-User-Email` 并传递 `X-User-Email: example@example.com` 的 HTTP 头将使用电子邮件 `example@example.com` 进行身份验证。

可选地，您还可以定义 `WEBUI_AUTH_TRUSTED_NAME_HEADER` 以确定使用受信任头创建的任何用户的名称。如果用户已经存在，则此操作无效。

### Tailscale Serve

[Tailscale Serve](https://tailscale.com/kb/1242/tailscale-serve) 允许您在您的 tailnet 内共享服务，并且 Tailscale 会将请求头 `Tailscale-User-Login` 设置为请求者的电子邮件地址。

以下是具有相应 Docker Compose 文件的示例服务配置，该文件启动 Tailscale 侧车，将 Open WebUI 暴露给 tailnet 并标记为 `open-webui` 和主机名 `open-webui`，并且可以到达 `https://open-webui.TAILNET_NAME.ts.net`。您需要创建一个具有设备写入权限的 OAuth 客户端以传递到 Tailscale 容器作为 `TS_AUTHKEY`。

```json title="tailscale/serve.json"
{
    "TCP": {
        "443": {
            "HTTPS": true
        }
    },
    "Web": {
        "${TS_CERT_DOMAIN}:443": {
            "Handlers": {
                "/": {
                    "Proxy": "http://open-webui:8080"
                }
            }
        }
    }
}

```

```yaml title="docker-compose.yaml"
---
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    volumes:
      - open-webui:/app/backend/data
    environment:
      - HOST=127.0.0.1
      - WEBUI_AUTH_TRUSTED_EMAIL_HEADER=Tailscale-User-Login
      - WEBUI_AUTH_TRUSTED_NAME_HEADER=Tailscale-User-Name
    restart: unless-stopped
  tailscale:
    image: tailscale/tailscale:latest
    environment:
      - TS_AUTH_ONCE=true
      - TS_AUTHKEY=${TS_AUTHKEY}
      - TS_EXTRA_ARGS=--advertise-tags=tag:open-webui
      - TS_SERVE_CONFIG=/config/serve.json
      - TS_STATE_DIR=/var/lib/tailscale
      - TS_HOSTNAME=open-webui
    volumes:
      - tailscale:/var/lib/tailscale
      - ./tailscale:/config
      - /dev/net/tun:/dev/net/tun
    cap_add:
      - net_admin
      - sys_module
    restart: unless-stopped

volumes:
  open-webui: {}
  tailscale: {}
```

:::warning

如果您在同一网络上下文中运行 Tailscale 和 Open WebUI，则默认情况下用户可以直接到达 Open WebUI，而无需通过 Serve 代理。
您需要使用 Tailscale 的 ACL 来限制对仅限端口 443 的访问。

:::

### Cloudflare Tunnel with Cloudflare Access

[Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/) 可以使用 [Cloudflare Access](https://developers.cloudflare.com/cloudflare-one/policies/access/) 保护 Open WebUI 与 SSO。
此功能由 Cloudflare 几乎未记录，但 `Cf-Access-Authenticated-User-Email` 由 Cloudflare 设置为经过身份验证的用户电子邮件地址。

以下是具有相应 Docker Compose 文件的示例配置，该文件设置 Cloudflare 侧车。
配置通过仪表板完成。
从仪表板中获取隧道令牌，将隧道后端设置为 `http://open-webui:8080`，并确保“使用访问保护”已检查和配置。

```yaml title="docker-compose.yaml"
---
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    volumes:
      - open-webui:/app/backend/data
    environment:
      - HOST=127.0.0.1
      - WEBUI_AUTH_TRUSTED_EMAIL_HEADER=Cf-Access-Authenticated-User-Email
    restart: unless-stopped
  cloudflared:
    image: cloudflare/cloudflared:latest
    environment:
      - TUNNEL_TOKEN=${TUNNEL_TOKEN}
    command: tunnel run
    restart: unless-stopped

volumes:
  open-webui: {}

```

### oauth2-proxy

[oauth2-proxy](https://oauth2-proxy.github.io/oauth2-proxy/) 是一个验证反向代理，实现了社交 OAuth 提供商和 OIDC 支持。

由于潜在的大量配置，以下是 Google OAuth 的潜在设置示例。
请参考 `oauth2-proxy` 的文档以进行详细设置和任何潜在的安全注意事项。

```yaml title="docker-compose.yaml"
services:
  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    volumes:
      - open-webui:/app/backend/data
    environment:
      - 'HOST=127.0.0.1'
      - 'WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-Forwarded-Email'
      - 'WEBUI_AUTH_TRUSTED_NAME_HEADER=X-Forwarded-User'
    restart: unless-stopped
  oauth2-proxy:
    image: quay.io/oauth2-proxy/oauth2-proxy:v7.6.0
    environment:
      OAUTH2_PROXY_HTTP_ADDRESS: 0.0.0.0:4180
      OAUTH2_PROXY_UPSTREAMS: http://open-webui:8080/
      OAUTH2_PROXY_PROVIDER: google
      OAUTH2_PROXY_CLIENT_ID: REPLACEME_OAUTH_CLIENT_ID
      OAUTH2_PROXY_CLIENT_SECRET: REPLACEME_OAUTH_CLIENT_ID
      OAUTH2_PROXY_EMAIL_DOMAINS: REPLACEME_ALLOWED_EMAIL_DOMAINS
      OAUTH2_PROXY_REDIRECT_URL: REPLACEME_OAUTH_CALLBACK_URL
      OAUTH2_PROXY_COOKIE_SECRET: REPLACEME_COOKIE_SECRET
      OAUTH2_PROXY_COOKIE_SECURE: "false"
    restart: unless-stopped
    ports:
      - 4180:4180/tcp
```


### Authentik

要配置 [Authentik](https://goauthentik.io/) OAuth 客户端，请参考 [文档](https://docs.goauthentik.io/docs/applications) 了解如何创建应用程序和 `OAuth2/OpenID Provider`。
允许的重定向 URI 必须包含 `<open-webui>/oauth/oidc/callback`。

在创建提供程序时，请注意 `App-name`、`Client-ID` 和 `Client-Secret`，并将其用于 open-webui 环境变量：

```
      - 'ENABLE_OAUTH_SIGNUP=true'
      - 'OAUTH_MERGE_ACCOUNTS_BY_EMAIL=true'
      - 'OAUTH_PROVIDER_NAME=Authentik'
      - 'OPENID_PROVIDER_URL=https://<authentik-url>/application/o/<App-name>/.well-known/openid-configuration'
      - 'OAUTH_CLIENT_ID=<Client-ID>'
      - 'OAUTH_CLIENT_SECRET=<Client-Secret>'
      - 'OAUTH_SCOPES=openid email profile'
      - 'OPENID_REDIRECT_URI=https://<open-webui>/oauth/oidc/callback'
```

### Authelia

[Authelia](https://www.authelia.com/) 可以配置为返回用于受信任头身份验证的标头。
文档可在 [这里](https://www.authelia.com/integration/trusted-header-sso/introduction/) 找到。

没有提供示例配置，因为部署 Authelia 的复杂性。
