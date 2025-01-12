---
sidebar_position: 19
title: "🔒 SSO：联合身份认证支持"
---

# 联合身份认证支持

Open WebUI 支持多种形式的联合身份认证：

1. OAuth2
    1. Google
    1. Microsoft
    1. OIDC
1. 可信头部

## OAuth

OAuth 有几个全局配置选项：

1. `ENABLE_OAUTH_SIGNUP` - 如果设为 `true`，允许在使用 OAuth 登录时创建账户。与 `ENABLE_SIGNUP` 不同。
1. `OAUTH_MERGE_ACCOUNTS_BY_EMAIL` - 允许登录到与 OAuth 提供商提供的电子邮件地址匹配的账户。
    - 这被认为是不安全的，因为并非所有 OAuth 提供商都验证电子邮件地址，可能导致账户被劫持。

### Google

要配置 Google OAuth 客户端，请参考 [Google 的文档](https://support.google.com/cloud/answer/6158849) 了解如何为**网络应用程序**创建 Google OAuth 客户端。
允许的重定向 URI 应包含 `<open-webui>/oauth/google/callback`。

需要以下环境变量：

1. `GOOGLE_CLIENT_ID` - Google OAuth 客户端 ID
1. `GOOGLE_CLIENT_SECRET` - Google OAuth 客户端密钥

### Microsoft

要配置 Microsoft OAuth 客户端，请参考 [Microsoft 的文档](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app) 了解如何为**网络应用程序**创建 Microsoft OAuth 客户端。
允许的重定向 URI 应包含 `<open-webui>/oauth/microsoft/callback`。

Microsoft OAuth 的支持目前仅限于单个租户，即单个 Entra 组织或个人 Microsoft 账户。

需要以下环境变量：

1. `MICROSOFT_CLIENT_ID` - Microsoft OAuth 客户端 ID
1. `MICROSOFT_CLIENT_SECRET` - Microsoft OAuth 客户端密钥
1. `MICROSOFT_CLIENT_TENANT_ID` - Microsoft 租户 ID - 对于个人账户使用 `9188040d-6c67-4c5b-b112-36a304b66dad`

### OIDC

可以配置任何支持 OIDC 的认证提供商。
需要 `email` 声明。
如果可用，将使用 `name` 和 `picture` 声明。
允许的重定向 URI 应包含 `<open-webui>/oauth/oidc/callback`。

使用以下环境变量：

1. `OAUTH_CLIENT_ID` - OIDC 客户端 ID
1. `OAUTH_CLIENT_SECRET` - OIDC 客户端密钥
1. `OPENID_PROVIDER_URL` - OIDC well known URL，例如 `https://accounts.google.com/.well-known/openid-configuration`
1. `OAUTH_PROVIDER_NAME` - 在 UI 上显示的提供商名称，默认为 SSO
1. `OAUTH_SCOPES` - 请求的作用域。默认为 `openid email profile`

### OAuth 角色管理

任何可以配置为在访问令牌中返回角色的 OAuth 提供商都可以用于管理 Open WebUI 中的角色。
要使用此功能，请将 `ENABLE_OAUTH_ROLE_MANAGEMENT` 设置为 `true`。
您可以配置以下环境变量以匹配 OAuth 提供商返回的角色：

1. `OAUTH_ROLES_CLAIM` - 包含角色的声明。默认为 `roles`。也可以是嵌套的，例如 `user.roles`。
1. `OAUTH_ALLOWED_ROLES` - 允许登录的角色列表（接收 open webui 角色 `user`），用逗号分隔。
1. `OAUTH_ADMIN_ROLES` - 允许以管理员身份登录的角色列表（接收 open webui 角色 `admin`），用逗号分隔。

:::info

如果更改已登录用户的角色，他们需要登出并重新登录才能获得新角色。

:::

## 可信头部

Open WebUI 能够将身份认证委托给一个认证反向代理，该代理在 HTTP 头部中传递用户的详细信息。
本页提供了几个示例配置。

:::danger

错误的配置可能允许用户以您的 Open WebUI 实例上的任何用户身份进行认证。
确保只允许认证代理访问 Open WebUI，例如设置 `HOST=127.0.0.1` 只监听回环接口。

:::

### 通用配置

当设置了 `WEBUI_AUTH_TRUSTED_EMAIL_HEADER` 环境变量时，Open WebUI 将使用指定头部的值作为用户的电子邮件地址，处理自动注册和登录。

例如，设置 `WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-User-Email` 并传递 HTTP 头部 `X-User-Email: example@example.com` 将使用电子邮件 `example@example.com` 进行认证。

可选地，您还可以定义 `WEBUI_AUTH_TRUSTED_NAME_HEADER` 来确定使用可信头部创建的任何用户的名称。如果用户已存在，这不会产生影响。

### Tailscale Serve

[Tailscale Serve](https://tailscale.com/kb/1242/tailscale-serve) 允许您在您的 tailnet 内共享服务，Tailscale 将设置头部 `Tailscale-User-Login` 为请求者的电子邮件地址。

以下是一个示例 serve 配置和相应的 Docker Compose 文件，它启动一个 Tailscale 边车，使用标签 `open-webui` 和主机名 `open-webui` 将 Open WebUI 暴露给 tailnet，可以在 `https://open-webui.TAILNET_NAME.ts.net` 访问。
您需要创建一个具有设备写入权限的 OAuth 客户端，并将其作为 `TS_AUTHKEY` 传递给 Tailscale 容器。

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

如果您在与 Open WebUI 相同的网络上下文中运行 Tailscale，则默认情况下用户将能够直接访问 Open WebUI 而无需通过 Serve 代理。
您需要使用 Tailscale 的 ACL 来限制只能访问 443 端口。

:::

### 带有 Cloudflare Access 的 Cloudflare Tunnel

[Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/) 可以与 [Cloudflare Access](https://developers.cloudflare.com/cloudflare-one/policies/access/) 一起使用，通过 SSO 保护 Open WebUI。
这在 Cloudflare 的文档中几乎没有提到，但 `Cf-Access-Authenticated-User-Email` 会设置为已认证用户的电子邮件地址。

以下是一个设置 Cloudflare 边车的示例 Docker Compose 文件。
配置通过仪表板完成。
从仪表板获取隧道令牌，将隧道后端设置为 `http://open-webui:8080`，并确保选中并配置了"使用 Access 保护"。

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

[oauth2-proxy](https://oauth2-proxy.github.io/oauth2-proxy/) 是一个认证反向代理，实现了社交 OAuth 提供商和 OIDC 支持。

考虑到可能的配置数量众多，以下是一个使用 Google OAuth 的潜在设置示例。
请参考 `oauth2-proxy` 的文档了解详细设置和任何潜在的安全注意事项。

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

要配置 [Authentik](https://goauthentik.io/) OAuth 客户端，请参考[文档](https://docs.goauthentik.io/docs/applications)了解如何创建应用程序和 `OAuth2/OpenID Provider`。
允许的重定向 URI 应包含 `<open-webui>/oauth/oidc/callback`。

创建提供商时，请注意 `App-name`、`Client-ID` 和 `Client-Secret`，并将其用于 open-webui 环境变量：

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

[Authelia](https://www.authelia.com/) 可以配置为返回一个头用于使用可信头部认证。
文档可用 [这里](https://www.authelia.com/integration/trusted-header-sso/introduction/)。

没有示例配置，因为部署 Authelia 的复杂性。
