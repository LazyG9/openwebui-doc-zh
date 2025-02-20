---
sidebar_position: 19
title: "🔒 SSO：联合认证支持"
---

# 联合认证支持

Open WebUI 支持以下几种联合认证方式：

1. OAuth2
    1. Google
    1. Microsoft
    1. Github
    1. OIDC
1. 可信头部认证

## OAuth

OAuth 系统提供以下全局配置选项：

1. `ENABLE_OAUTH_SIGNUP` - 设置为 `true` 时，允许用户在使用 OAuth 登录时创建新账号。此选项独立于 `ENABLE_SIGNUP`。
1. `OAUTH_MERGE_ACCOUNTS_BY_EMAIL` - 允许用户使用与 OAuth 提供商提供的邮箱地址匹配的已有账号登录。
    - 注意：这种方式存在安全风险，因为并非所有 OAuth 提供商都会验证邮箱地址，可能导致账号被恶意劫持。

### Google

如需配置 Google OAuth 客户端，请参考 [Google 官方文档](https://support.google.com/cloud/answer/6158849) 了解如何为**网络应用**创建 Google OAuth 客户端。
请确保将 `<open-webui>/oauth/google/callback` 添加到允许的重定向 URI 列表中。

系统需要配置以下环境变量：

1. `GOOGLE_CLIENT_ID` - Google OAuth 客户端 ID
1. `GOOGLE_CLIENT_SECRET` - Google OAuth 客户端密钥

### Microsoft

要配置 Microsoft OAuth 客户端，请参考 [Microsoft 官方文档](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app) 了解如何为**网络应用**创建 Microsoft OAuth 客户端。
系统要求将 `<open-webui>/oauth/microsoft/callback` 添加到允许的重定向 URI 列表中。

请注意：目前 Microsoft OAuth 支持仅限于单个租户，即单个 Entra 组织或个人 Microsoft 账号。

系统需要配置以下环境变量：

1. `MICROSOFT_CLIENT_ID` - Microsoft OAuth 客户端 ID
1. `MICROSOFT_CLIENT_SECRET` - Microsoft OAuth 客户端密钥
1. `MICROSOFT_CLIENT_TENANT_ID` - Microsoft 租户 ID（对于个人账号，请使用 `9188040d-6c67-4c5b-b112-36a304b66dad`）

### Github

要配置 Github OAuth 客户端，请参考 [Github 官方文档](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps) 了解如何为**网络应用**创建 OAuth App 或 Github App。
系统要求将 `<open-webui>/oauth/github/callback` 添加到允许的重定向 URI 列表中。

系统需要配置以下环境变量：

1. `GITHUB_CLIENT_ID` - Github OAuth App 客户端 ID
1. `GITHUB_CLIENT_SECRET` - Github OAuth App 客户端密钥

### OIDC

系统支持配置任何兼容 OIDC 标准的认证提供商。
配置要求：
- 必须提供 `email` 声明
- 如果可用，系统将使用 `name` 和 `picture` 声明
- 必须将 `<open-webui>/oauth/oidc/callback` 添加到允许的重定向 URI 列表中

系统使用以下环境变量进行配置：

1. `OAUTH_CLIENT_ID` - OIDC 客户端 ID
1. `OAUTH_CLIENT_SECRET` - OIDC 客户端密钥
1. `OPENID_PROVIDER_URL` - OIDC well known URL，例如 `https://accounts.google.com/.well-known/openid-configuration`
1. `OAUTH_PROVIDER_NAME` - 在用户界面上显示的提供商名称，默认为 SSO
1. `OAUTH_SCOPES` - 需要请求的权限范围，默认为 `openid email profile`

### OAuth 角色管理

系统支持使用任何能在访问令牌中返回角色信息的 OAuth 提供商来管理 Open WebUI 中的用户角色。
如需启用此功能，请将 `ENABLE_OAUTH_ROLE_MANAGEMENT` 环境变量设置为 `true`。
您可以通过配置以下环境变量来匹配 OAuth 提供商返回的角色信息：

1. `OAUTH_ROLES_CLAIM` - 指定包含角色信息的声明字段。默认值为 `roles`。支持嵌套格式，如 `user.roles`
1. `OAUTH_ALLOWED_ROLES` - 以逗号分隔的角色列表，这些角色将被授予 Open WebUI 的 `user` 权限
1. `OAUTH_ADMIN_ROLES` - 以逗号分隔的角色列表，这些角色将被授予 Open WebUI 的 `admin` 权限

:::info

重要提示：当用户的角色被更改后，需要重新登录才能使新的角色权限生效。

:::

### OAuth 群组管理

系统支持使用任何能在访问令牌中返回群组信息的 OAuth 提供商来管理 Open WebUI 中的用户群组。
如需启用此功能，请将 `ENABLE_OAUTH_GROUP_MANAGEMENT` 环境变量设置为 `true`。
您可以通过配置以下环境变量来匹配 OAuth 提供商返回的群组信息：

1. `OAUTH_GROUP_CLAIM` - 指定包含群组信息的声明字段。默认值为 `groups`。支持嵌套格式，如 `user.memberOf`

:::warning
注意：管理员用户的群组信息不会被自动更新
:::

:::info

重要提示：当用户的群组被更改后，需要重新登录才能使新的群组设置生效。

:::

## 可信头部认证

Open WebUI 支持将身份认证委托给认证代理服务器，该代理通过 HTTP 头部传递用户信息。
以下提供了几个配置示例供参考。

:::danger

安全警告：配置不当可能导致用户能够以任意身份登录您的 Open WebUI 实例。
为确保安全，请务必只允许认证代理访问 Open WebUI。例如，您可以通过设置 `HOST=127.0.0.1` 来限制系统只监听本地回环接口。

:::

### 通用配置

当配置了 `WEBUI_AUTH_TRUSTED_EMAIL_HEADER` 环境变量后，Open WebUI 将使用该指定头部中的值作为用户的电子邮件地址，并自动完成注册和登录流程。

配置示例：如果设置 `WEBUI_AUTH_TRUSTED_EMAIL_HEADER=X-User-Email` 并在 HTTP 请求中包含头部 `X-User-Email: example@example.com`，系统将使用 `example@example.com` 作为用户邮箱进行身份认证。

可选配置：您还可以通过设置 `WEBUI_AUTH_TRUSTED_NAME_HEADER` 来指定新用户的显示名称。请注意，此设置仅对新创建的用户生效，对已存在的用户无影响。

### Tailscale Serve

[Tailscale Serve](https://tailscale.com/kb/1242/tailscale-serve) 允许您在 tailnet 网络内共享服务。当用户通过 Tailscale 访问服务时，Tailscale 会自动在 `Tailscale-User-Login` 头部中设置访问者的电子邮件地址。

下面提供了一个完整的配置示例，包含 Tailscale 服务配置和 Docker Compose 文件。这个配置将启动一个 Tailscale 边车容器，通过标签 `open-webui` 和主机名 `open-webui` 将 Open WebUI 暴露到 tailnet 网络中。配置完成后，服务可以通过 `https://open-webui.TAILNET_NAME.ts.net` 访问。

配置前准备：您需要创建一个具有设备写入权限的 OAuth 客户端，并将获得的密钥作为 `TS_AUTHKEY` 传递给 Tailscale 容器。

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

安全提醒：如果您在与 Open WebUI 相同的网络环境中运行 Tailscale，用户默认可以绕过 Serve 代理直接访问 Open WebUI。
为了确保安全，您需要使用 Tailscale 的访问控制列表（ACL）来限制只允许通过 443 端口访问服务。

:::

### Cloudflare Tunnel 与 Cloudflare Access 集成

您可以将 [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/) 与 [Cloudflare Access](https://developers.cloudflare.com/cloudflare-one/policies/access/) 结合使用，为 Open WebUI 提供 SSO 保护。
虽然这个功能在 Cloudflare 的文档中描述较少，但系统会自动将认证用户的电子邮件地址设置在 `Cf-Access-Authenticated-User-Email` 头部中。

下面提供了一个使用 Docker Compose 配置 Cloudflare 边车服务的示例文件。
配置过程需要在 Cloudflare 仪表板中完成，具体步骤如下：
1. 从仪表板获取隧道令牌
2. 将隧道后端设置为 `http://open-webui:8080`
3. 确保启用并正确配置了"Protect with Access"选项

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

### oauth2-proxy 配置

[oauth2-proxy](https://oauth2-proxy.github.io/oauth2-proxy/) 是一个功能强大的认证反向代理，支持多种社交平台的 OAuth 认证以及 OIDC 协议。

由于 oauth2-proxy 支持多种配置方式，这里我们提供一个基于 Google OAuth 的配置示例作为参考。
在实际部署前，请务必查阅 `oauth2-proxy` 的官方文档，了解详细的配置说明和相关的安全注意事项。

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

### Authentik 配置

要配置 [Authentik](https://goauthentik.io/) OAuth 客户端，请参考[官方文档](https://docs.goauthentik.io/docs/applications)了解如何创建应用程序和配置 `OAuth2/OpenID Provider`。
系统要求将 `<open-webui>/oauth/oidc/callback` 添加到允许的重定向 URI 列表中。

在创建认证提供商时，请注意记录以下信息：
- `App-name`（应用名称）
- `Client-ID`（客户端 ID）
- `Client-Secret`（客户端密钥）

这些信息将用于设置 Open WebUI 的环境变量，配置示例如下：

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

### Authelia 配置

[Authelia](https://www.authelia.com/) 支持配置可信头部认证。
如需了解详细配置说明，请参考[官方文档](https://www.authelia.com/integration/trusted-header-sso/introduction/)。

注意：由于 Authelia 的部署配置较为复杂，此处不提供具体的配置示例。建议用户根据实际需求参考官方文档进行配置。
