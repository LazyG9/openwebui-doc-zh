---
sidebar_position: 10
title: "SearXNG（开源元搜索引擎）"
---

:::warning
本教程是来自社区的贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据您的具体需求来自定义 Open WebUI 的示例。想要参与贡献？请查看我们的贡献指南。
:::

本指南介绍如何在 Docker 环境中配置 SearXNG 来为 Open WebUI 提供网页搜索功能。

## SearXNG (Docker)

> "**SearXNG 是一个自由开源的互联网元搜索引擎，它可以聚合多个搜索服务和数据库的结果。它完全不会跟踪或分析用户的行为。**"

## 1. SearXNG 配置说明

请按照以下步骤为 Open WebUI 配置 SearXNG：

**步骤 1：克隆并准备 SearXNG Docker 环境**

1. 创建 SearXNG Docker 目录

首先克隆 searxng-docker 仓库。该目录将存放所有 SearXNG 配置文件。详细配置说明请参考 [SearXNG 官方文档](https://docs.searxng.org/)。

```bash
git clone https://github.com/searxng/searxng-docker.git
```

进入 `searxng-docker` 目录：

```bash
cd searxng-docker
```

**步骤 2：配置 `.env` 环境文件**

1. 编辑 `.env` 文件，取消 `SEARXNG_HOSTNAME` 的注释并设置相应的值：

```bash
# 默认配置为监听 https://localhost
# 如需修改，请：
# * 取消 SEARXNG_HOSTNAME 的注释，并将 <host> 替换为您的 SearXNG 主机名
# * 取消 LETSENCRYPT_EMAIL 的注释，并填入您的邮箱（用于申请 Let's Encrypt 证书）

SEARXNG_HOSTNAME=localhost:8080/
# LETSENCRYPT_EMAIL=<email>

# 可选配置：
# 如果您的服务器规模较小或较大，可能需要调整 uwsgi 工作进程数和每个进程的线程数
# 工作进程数越多，可以同时处理的搜索请求就越多，但也会消耗更多系统资源

# SEARXNG_UWSGI_WORKERS=4
# SEARXNG_UWSGI_THREADS=4
```

**步骤 3：修改 Docker Compose 配置**

3. 修改 `docker-compose.yaml` 文件以移除 localhost 限制：

```bash
sed -i "s/127.0.0.1:8080/0.0.0.0:8080/"
```

**步骤 4：设置文件权限**

4. 在根目录执行以下命令，确保容器有权限创建配置文件：

```bash
sudo chmod a+rwx searxng-docker/searxng
```

**步骤 5：创建限流配置文件**

5. 创建 `searxng-docker/searxng/limiter.toml` 配置文件，设置较为宽松的访问限制：

<details>
<summary>searxng-docker/searxng/limiter.toml</summary>

```bash
# 此文件用于更新默认的限流配置
# 详见 https://github.com/searxng/searxng/blob/master/searx/botdetection/limiter.toml

[botdetection.ip_limit]
# 在 IP 限制中禁用 link_token 验证
link_token = false

[botdetection.ip_lists]
block_ip = []  # 禁止访问的 IP 列表
pass_ip = []   # 允许访问的 IP 列表
```

</details>

**步骤 6：清理默认配置文件**

6. 删除默认的 `settings.yml` 文件（如果存在），系统会在首次启动时自动生成新的配置：

```bash
rm searxng-docker/searxng/settings.yml
```

**步骤 7：初始化配置文件**

:::note
首次运行时，需要临时从 `docker-compose.yaml` 的 `searxng` 服务配置中移除 `cap_drop: - ALL` 选项，这样才能成功创建 `/etc/searxng/uwsgi.ini` 文件。这是因为 `cap_drop: - ALL` 会移除所有容器权限，包括创建配置文件所需的权限。完成首次运行后，请务必将 `cap_drop: - ALL` 添加回配置文件以确保安全性。
:::

7. 临时启动容器以生成初始配置文件：

```bash
docker compose up -d ; sleep 10 ; docker compose down
```

**步骤 8：配置搜索功能**

8. 在 `searxng-docker/searxng/settings.yml` 中添加必要的搜索结果格式：

```bash
sed -i 's/formats: \[\"html\"\/]/formats: [\"html\", \"json\"]/' searxng-docker/searxng/settings.yml
```

生成安全的随机密钥：

```bash
sed -i "s|ultrasecretkey|$(openssl rand -hex 32)|g" searxng-docker/searxng/settings.yml
```

Windows 用户可使用此 PowerShell 脚本生成密钥：

```powershell
$randomBytes = New-Object byte[] 32
(New-Object Security.Cryptography.RNGCryptoServiceProvider).GetBytes($randomBytes)
$secretKey = -join ($randomBytes | ForEach-Object { "{0:x2}" -f $_ })
(Get-Content searxng-docker/searxng/settings.yml) -replace 'ultrasecretkey', $secretKey | Set-Content searxng-docker/searxng/settings.yml
```

设置服务端口（本例中使用 8080）：

```bash
sed -i 's/port: 8080/port: 8080/' searxng-docker/searxng/settings.yml
```

配置监听地址：

```bash
sed -i 's/bind_address: "0.0.0.0"/bind_address: "127.0.0.1"/' searxng-docker/searxng/settings.yml
```

#### 核心配置文件说明

#### searxng-docker/searxng/settings.yml（配置示例）

默认的 `settings.yml` 包含多项引擎配置。以下是主要配置项说明：

<details>
<summary>searxng-docker/searxng/settings.yml</summary>

```yaml
# 详细说明见 https://docs.searxng.org/admin/settings/settings.html#settings-use-default-settings
use_default_settings: true

server:
  # base_url 通过环境变量 SEARXNG_BASE_URL 定义，详见 .env 和 docker-compose.yml
  secret_key: "ultrasecretkey"  # 重要：请修改此密钥！
  limiter: true  # 私有实例可以禁用此限制
  image_proxy: true  # 启用图片代理
  port: 8080      # 服务端口
  bind_address: "0.0.0.0"  # 监听地址

ui:
  static_use_hash: true  # 静态资源使用哈希

search:
  safe_search: 0         # 安全搜索级别
  autocomplete: ""       # 自动完成配置
  default_lang: ""       # 默认语言
  formats:              # 支持的返回格式
    - html
    - json  # json 格式为必需
  # 通过删除格式来限制访问，使用小写
  # formats: [html, csv, json, rss]
redis:
  # Redis 数据库连接 URL，可被环境变量 ${SEARXNG_REDIS_URL} 覆盖
  # 详见 https://docs.searxng.org/admin/settings/settings_redis.html#settings-redis
  url: redis://redis:6379/0
```

注意：SearXNG 的 settings.yml 中配置的端口必须与 docker-compose.yml 中的端口保持一致。

</details>

**步骤 9：配置 uWSGI**

9. 确保 `searxng-docker/searxng/uwsgi.ini` 文件包含以下配置：

<details>
<summary>searxng-docker/searxng/uwsgi.ini</summary>

```ini
[uwsgi]
# 进程权限配置
uid = searxng
gid = searxng

# 工作进程配置（通常设置为 CPU 核心数）
# 默认值：%k（CPU 核心数，见 Dockerfile）
workers = %k

# 每个工作进程的线程数
# 默认值：4（见 Dockerfile）
threads = 4

# 创建的 socket 文件权限
chmod-socket = 666

# 进程和插件配置
single-interpreter = true  # 单解释器模式
master = true            # 主进程模式
plugin = python3         # Python3 插件
lazy-apps = true        # 延迟加载应用
enable-threads = 4      # 启用线程支持

# 应用模块配置
module = searx.webapp

# 路径配置
pythonpath = /usr/local/searxng/
chdir = /usr/local/searxng/searx/

# 进程命名
auto-procname = true

# 日志配置
disable-logging = true  # 禁用常规日志
log-5xx = true         # 记录 5xx 错误

# 请求配置
buffer-size = 8192     # 请求缓冲区大小

# 连接处理
# 详见 https://github.com/searx/searx-docker/issues/24
add-header = Connection: close  # 禁用长连接

# 静态文件处理
static-map = /static=/usr/local/searxng/searx/static  # 静态文件映射
static-expires = /* 86400    # 静态文件缓存时间（1天）
static-gzip-all = True      # 启用 gzip 压缩
offload-threads = 4         # 静态文件处理线程数
```
</details>

## 2. 替代配置方案

如果您不想修改默认配置，也可以简单地创建一个空的 `searxng-docker` 文件夹，然后按照以下说明进行设置。

### Docker Compose 配置

将以下环境变量添加到您的 Open WebUI `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True
      RAG_WEB_SEARCH_ENGINE: "searxng"
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
      SEARXNG_QUERY_URL: "http://searxng:8080/search?q=<query>"
```

创建 SearXNG 的 `.env` 文件：

```
# SearXNG
SEARXNG_HOSTNAME=localhost:8080/
```

然后，将以下内容添加到 SearXNG 的 `docker-compose.yaml` 文件中：

```yaml
services:
  searxng:
    container_name: searxng
    image: searxng/searxng:latest
    ports:
      - "8080:8080"
    volumes:
      - ./searxng:/etc/searxng:rw
    env_file:
      - .env
    restart: unless-stopped
    cap_drop:
      - ALL
    cap_add:
      - CHOWN
      - SETGID
      - SETUID
      - DAC_OVERRIDE
    logging:
      driver: "json-file"
      options:
        max-size: "1m"
        max-file: "1"
```

现在您可以使用以下命令启动服务：

```bash
docker compose up -d
```

:::note
首次运行时，需要从 `docker-compose.yaml` 文件中的 `searxng` 服务配置中移除 `cap_drop: - ALL` 选项，这样才能成功创建 `/etc/searxng/uwsgi.ini` 文件。这是因为 `cap_drop: - ALL` 会移除所有容器权限，包括创建配置文件所需的权限。完成首次运行后，请务必将 `cap_drop: - ALL` 添加回配置文件以确保安全性。
:::

或者，您也可以直接使用 `docker run` 命令运行 SearXNG：

```bash
docker run --name searxng --env-file .env -v ./searxng:/etc/searxng:rw -p 8080:8080 --restart unless-stopped --cap-drop ALL --cap-add CHOWN --cap-add SETGID --cap-add SETUID --cap-add DAC_OVERRIDE --log-driver json-file --log-opt max-size=1m --log-opt max-file=1 searxng/searxng:latest
```

## 3. 验证连接

在命令行界面中验证您的 Open WebUI 容器实例是否可以连接到 SearXNG：

```bash
docker exec -it open-webui curl http://host.docker.internal:8080/search?q=this+is+a+test+query&format=json
```

## 4. 图形界面配置

1. 导航到：`管理面板` -> `设置` -> `网页搜索`
2. 启用 `网页搜索` 开关
3. 在下拉菜单中将 `网页搜索引擎` 设置为 `searxng`
4. 将 `Searxng 查询 URL` 设置为以下示例之一：

* `http://searxng:8080/search?q=<query>` （使用容器名称和暴露的端口，适用于基于 Docker 的设置）
* `http://host.docker.internal:8080/search?q=<query>` （使用 `host.docker.internal` DNS 名称和主机端口，适用于基于 Docker 的设置）
* `http://<searxng.local>/search?q=<query>` （使用本地域名，适用于本地网络访问）
* `https://<search.domain.com>/search?q=<query>` （使用自定义域名访问自托管的 SearXNG 实例，适用于公共或私有访问）

**请注意 `/search?q=<query>` 部分是必需的。**
5. 根据需要调整 `搜索结果数量` 和 `并发请求数` 的值
6. 保存更改

![SearXNG 图形界面配置](/images/tutorial_searxng_config.png)

## 5. 在聊天中使用网页搜索

要访问网页搜索功能，请点击消息输入框旁边的 + 号。

在这里您可以打开/关闭网页搜索功能。

![网页搜索 UI 开关](/images/web_search_toggle.png)

按照这些步骤，您就可以成功地在 Open WebUI 中设置 SearXNG，从而能够使用 SearXNG 引擎进行网页搜索。

#### 注意事项

您需要在每个聊天中手动打开/关闭此功能。

此设置仅在当前会话中有效，例如：刷新页面或切换到其他聊天时，设置会被关闭。
