---
sidebar_position: 5
title: "🌐 网络搜索"
---

:::warning
本教程是社区贡献内容，不受 Open WebUI 团队支持。它仅作为如何根据您的特定用例自定义 Open WebUI 的演示。想要贡献？请查看贡献教程。
:::

## 🌐 网络搜索

本指南提供了如何使用各种搜索引擎在 Open WebUI 中设置网络搜索功能的说明。

## SearXNG (Docker)

> "**SearXNG 是一个免费的互联网元搜索引擎，它聚合了来自各种搜索服务和数据库的结果。用户既不会被跟踪也不会被分析。**"

## 1. SearXNG 配置

要为 Open WebUI 优化配置 SearXNG，请按照以下步骤操作：

**步骤 1：`git clone` SearXNG Docker 并导航到文件夹：**

1. 创建新目录 `searxng-docker`

克隆 searxng-docker 仓库。该文件夹将包含您的 SearXNG 配置文件。有关配置说明，请参阅 [SearXNG 文档](https://docs.searxng.org/)。

```bash
git clone https://github.com/searxng/searxng-docker.git
```

导航到 `searxng-docker` 仓库：

```bash
cd searxng-docker
```

**步骤 2：定位并修改 `.env` 文件：**

1. 取消注释 `.env` 文件中的 `SEARXNG_HOSTNAME` 并进行相应设置：

```bash
# 默认监听 https://localhost
# 要更改此设置：
# * 取消注释 SEARXNG_HOSTNAME，并将 <host> 替换为 SearXNG 主机名
# * 取消注释 LETSENCRYPT_EMAIL，并将 <email> 替换为您的邮箱（用于创建 Let's Encrypt 证书）

SEARXNG_HOSTNAME=localhost:8080/
# LETSENCRYPT_EMAIL=<email>

# 可选：
# 如果您运行的是非常小型或非常大型的实例，您可能想要更改使用的 uwsgi 工作进程数量和每个工作进程的线程数
# 更多的工作进程（= 进程）意味着可以同时处理更多的搜索请求，但也会导致更多的资源使用

# SEARXNG_UWSGI_WORKERS=4
# SEARXNG_UWSGI_THREADS=4
```

**步骤 3：修改 `docker-compose.yaml` 文件**

3. 通过修改 `docker-compose.yaml` 文件来删除 `localhost` 限制：

```bash
sed -i "s/127.0.0.1:8080/0.0.0.0:8080/"
```

**步骤 4：授予必要的权限**

4. 允许容器创建新的配置文件，通过在根目录下运行以下命令：

```bash
sudo chmod a+rwx searxng-docker/searxng
```

**步骤 5：创建一个非限制性的 `limiter.toml` 文件**

5. 创建一个非限制性的 `searxng-docker/searxng/limiter.toml` 配置文件：

<details>
<summary>searxng-docker/searxng/limiter.toml</summary>

```bash
# 此配置文件更新默认配置文件
# 参见 https://github.com/searxng/searxng/blob/master/searx/botdetection/limiter.toml

[botdetection.ip_limit]
# 在 ip_limit 方法中激活 link_token 方法
link_token = false

[botdetection.ip_lists]
block_ip = []
pass_ip = []
```

</details>

**步骤 6：删除默认的 `settings.yml` 文件**

6. 如果存在默认的 `searxng-docker/searxng/settings.yml` 文件，则删除它，因为它将在第一次启动 SearXNG 时重新生成：

```bash
rm searxng-docker/searxng/settings.yml
```

**步骤 7：创建一个新的 `settings.yml` 文件**

:::note
在第一次运行时，必须从 `docker-compose.yaml` 文件中删除 `cap_drop: - ALL` 以使 `searxng` 服务成功创建 `/etc/searxng/uwsgi.ini`。这是必要的，因为 `cap_drop: - ALL` 指令会删除所有功能，包括创建 `uwsgi.ini` 文件所需的功能。第一次运行后，应该将 `cap_drop: - ALL` 添加到 `docker-compose.yaml` 文件中以确保安全。
:::

7. 短暂启动容器以生成新的 settings.yml 文件：

```bash
docker compose up -d ; sleep 10 ; docker compose down
```

**步骤 8：添加格式并更新端口号**

8. 将 HTML 和 JSON 格式添加到 `searxng-docker/searxng/settings.yml` 文件中：

```bash
sed -i 's/formats: \[\"html\"\/]/formats: [\"html\", \"json\"]/' searxng-docker/searxng/settings.yml
```

生成 SearXNG 实例的秘密密钥：

```bash
sed -i "s|ultrasecretkey|$(openssl rand -hex 32)|g" searxng-docker/searxng/settings.yml
```

Windows 用户可以使用以下 powershell 脚本来生成秘密密钥：

```powershell
$randomBytes = New-Object byte[] 32
(New-Object Security.Cryptography.RNGCryptoServiceProvider).GetBytes($randomBytes)
$secretKey = -join ($randomBytes | ForEach-Object { "{0:x2}" -f $_ })
(Get-Content searxng-docker/searxng/settings.yml) -replace 'ultrasecretkey', $secretKey | Set-Content searxng-docker/searxng/settings.yml
```

更新 settings.yml 文件中的端口号以匹配您之前设置的端口号（在本例中为 `8080`）：

```bash
sed -i 's/port: 8080/port: 8080/' searxng-docker/searxng/settings.yml
```

更改 `bind_address` 以满足您的需求：

```bash
sed -i 's/bind_address: "0.0.0.0"/bind_address: "127.0.0.1"/' searxng-docker/searxng/settings.yml
```

#### 配置文件

#### searxng-docker/searxng/settings.yml（提取）

默认的 `settings.yml` 文件包含许多引擎设置。以下是默认 `settings.yml` 文件的提取：

<details>
<summary>searxng-docker/searxng/settings.yml</summary>

```yaml
# 参见 https://docs.searxng.org/admin/settings/settings.html#settings-use-default-settings
use_default_settings: true

server:
  # base_url 在环境变量 SEARXNG_BASE_URL 中定义，请参见 .env 和 docker-compose.yml
  secret_key: "ultrasecretkey"  # 请更改此值！
  limiter: true  # 可以在私有实例中禁用
  image_proxy: true
  port: 8080
  bind_address: "0.0.0.0"

ui:
  static_use_hash: true

search:
  safe_search: 0
  autocomplete: ""
  default_lang: ""
  formats:
    - html
    - json # json 是必需的
  # 删除格式以拒绝访问，使用小写。
  # formats: [html, csv, json, rss]
redis:
  # 连接 redis 数据库的 URL。被 ${SEARXNG_REDIS_URL} 覆盖。
  # https://docs.searxng.org/admin/settings/settings_redis.html#settings-redis
  url: redis://redis:6379/0
```

在 settings.yml 文件中，SearXNG 的端口号应与 docker-compose.yml 文件中的端口号匹配。

</details>

**步骤 9：更新 `uwsgi.ini` 文件**

9. 确保您的 `searxng-docker/searxng/uwsgi.ini` 文件与以下内容匹配：

<details>
<summary>searxng-docker/searxng/uwsgi.ini</summary>

```ini
[uwsgi]
# 谁将运行代码
uid = searxng
gid = searxng

# 工作进程数量（通常是 CPU 数量）
# 默认值：%k（= CPU 核心数，参见 Dockerfile）
workers = %k

# 每个工作进程的线程数
# 默认值：4（参见 Dockerfile）
threads = 4

# 创建的套接字的权限
chmod-socket = 666

# 要使用的插件和解释器配置
single-interpreter = true
master = true
plugin = python3
lazy-apps = true
enable-threads = 4

# 要导入的模块
module = searx.webapp

# 虚拟环境和 python 路径
pythonpath = /usr/local/searxng/
chdir = /usr/local/searxng/searx/

# 自动将进程名设置为有意义的名称
auto-procname = true

# 为了隐私禁用请求日志记录
disable-logging = true
log-5xx = true

# 设置请求的最大大小（不包括请求体）
buffer-size = 8192

# 无保持连接
# 参见 https://github.com/searx/searx-docker/issues/24
add-header = Connection: close

# uwsgi 提供静态文件
static-map = /static=/usr/local/searxng/searx/static
# 过期时间设置为一天
static-expires = /* 86400
```

</details>

## 2. 替代设置

或者，如果您不想修改默认配置，可以简单地创建一个空 `searxng-docker` 文件夹并按照其余设置步骤进行操作。

### Docker Compose 设置

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

为 SearXNG 创建一个 `.env` 文件：

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

您的堆栈已准备好启动：

```bash
docker compose up -d
```

:::note
在第一次运行时，必须从 `docker-compose.yaml` 文件中删除 `cap_drop: - ALL` 以使 `searxng` 服务成功创建 `/etc/searxng/uwsgi.ini`。这是必要的，因为 `cap_drop: - ALL` 指令会删除所有功能，包括创建 `uwsgi.ini` 文件所需的功能。第一次运行后，应该将 `cap_drop: - ALL` 添加到 `docker-compose.yaml` 文件中以确保安全。
:::

或者，您可以直接使用 `docker run` 运行 SearXNG：

```bash
docker run --name searxng --env-file .env -v ./searxng:/etc/searxng:rw -p 8080:8080 --restart unless-stopped --cap-drop ALL --cap-add CHOWN --cap-add SETGID --cap-add SETUID --cap-add DAC_OVERRIDE --log-driver json-file --log-opt max-size=1m,max-file=1 searxng/searxng:latest
```

## 3. 确认连接性

确认从您的 Open WebUI 容器实例连接到 SearXNG，在您的命令行界面中：

```bash
docker exec -it open-webui curl http://host.docker.internal:8080/search?q=this+is+a+test+query&format=json
```

## 4. GUI 配置

1. 导航到：`Admin Panel` -> `Settings` -> `Web Search`
2. 切换 `Enable Web Search`
3. 从下拉菜单中设置 `Web Search Engine` 为 `searxng`
4. 设置 `Searxng Query URL` 为以下示例之一：

* `http://searxng:8080/search?q=<query>`（使用容器名称和暴露的端口，适用于 Docker 设置）
* `http://host.docker.internal:8080/search?q=<query>`（使用 `host.docker.internal` DNS 名称和主机端口，适用于 Docker 设置）
* `http://<searxng.local>/search?q=<query>`（使用本地域名，适用于本地网络访问）
* `https://<search.domain.com>/search?q=<query>`（使用自定义域名，适用于自托管的 SearXNG 实例，适用于公共或私有访问）

**请注意 `/search?q=<query>` 部分是必需的。**
5. 相应调整 `Search Result Count` 和 `Concurrent Requests` 值
6. 保存更改

![SearXNG GUI Configuration](/img/tutorial_searxng_config.png)

## 5. 在聊天中使用网络搜索

要访问网络搜索，请单击消息输入字段旁边的 +。

在这里，您可以切换网络搜索的开/关。

![Web Search UI Toggle](/img/web_search_toggle.png)

通过按照这些步骤，您将成功设置 SearXNG 与 Open WebUI，使您能够使用 SearXNG 引擎执行网络搜索。

#### 注意

您必须在聊天中明确切换此开/关。

这是基于会话的，例如重新加载页面或切换到另一个聊天会话将关闭。

## Google PSE API

### 设置

1. 前往 Google Developers，使用 [Programmable Search Engine](https://developers.google.com/custom-search) 并登录或创建帐户。
2. 前往 [控制面板](https://programmablesearchengine.google.com/controlpanel/all) 并单击 `Add` 按钮
3. 输入搜索引擎名称，设置其他属性以满足您的需求，验证您不是机器人并单击 `Create` 按钮。
4. 生成 `API key` 并获取 `Search engine ID`。（在创建引擎后可用）
5. 使用 `API key` 和 `Search engine ID`，打开 `Open WebUI Admin panel` 并单击 `Settings` 选项卡，然后单击 `Web Search`
6. 启用 `Web search` 并设置 `Web Search Engine` 为 `google_pse`
7. 填写 `Google PSE API Key` 并填写 `Google PSE Engine Id`（第 4 步）
8. 单击 `Save`

![Open WebUI Admin panel](/img/tutorial_google_pse1.png)

#### 注意

您必须在提示字段中启用 `Web search`，使用加号（+）按钮。
搜索网络 ;-)

![enable Web search](/img/tutorial_google_pse2.png)

## Brave API

### Docker Compose 设置

将以下环境变量添加到您的 Open WebUI `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True
      RAG_WEB_SEARCH_ENGINE: "brave"
      BRAVE_SEARCH_API_KEY: "YOUR_API_KEY"
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
```

## Mojeek Search API

### 设置

1. 请访问 [Mojeek Search API 页面](https://www.mojeek.com/services/search/web-search-api/) 以获取 `API key`
2. 使用 `API key`，打开 `Open WebUI Admin panel` 并单击 `Settings` 选项卡，然后单击 `Web Search`
3. 启用 `Web search` 并设置 `Web Search Engine` 为 `mojeek`
4. 填写 `Mojeek Search API Key` 并填写 `API key`
5. 单击 `Save`

### Docker Compose 设置

将以下环境变量添加到您的 Open WebUI `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True
      RAG_WEB_SEARCH_ENGINE: "mojeek"
      BRAVE_SEARCH_API_KEY: "YOUR_MOJEEK_API_KEY"
      RAG_WEB_SEARCH_RESULT_COUNT: 3
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10
```

## SearchApi API

[SearchApi](https://searchapi.io) 是一个实时 SERP API 集合。任何现有的或即将推出的 SERP 引擎，只要返回 `organic_results`，都支持。默认的网络搜索引擎是 `google`，但可以更改为 `bing`、`baidu`、`google_news`、`bing_news`、`google_scholar`、`google_patents` 和其它。

### 设置

1. 前往 [SearchApi](https://searchapi.io)，并登录或创建新帐户。
2. 前往 `Dashboard` 并复制 API key。
3. 使用 `API key`，打开 `Open WebUI Admin panel` 并单击 `Settings` 选项卡，然后单击 `Web Search`。
4. 启用 `Web search` 并设置 `Web Search Engine` 为 `searchapi`。
5. 填写 `SearchApi API Key` 并填写从 [SearchApi](https://www.searchapi.io/) 仪表板复制的 API key。
6. [可选] 输入您想要查询的 `SearchApi 引擎` 名称。例如，`google`、`bing`、`baidu`、`google_news`、`bing_news`、`google_videos`、`google_scholar` 和 `google_patents`。默认情况下，它设置为 `google`。
7. 单击 `Save`。

![Open WebUI Admin panel](/img/tutorial_searchapi_search.png)

#### 注意

您必须在提示字段中启用 `Web search`，使用加号（+）按钮来搜索网络使用 [SearchApi](https://www.searchapi.io/) 引擎。

![enable Web search](/img/enable_web_search.png)

## Kagi API

Coming Soon

### 设置

## Serpstack API

Coming Soon

### 设置

## Serper API

Coming Soon

### 设置

## Serply API

Coming Soon

### 设置

## DuckDuckGo API

### 设置

无需设置即可使用 DuckDuckGo API 进行 Open WebUI 的内置网络搜索！DuckDuckGo 在 Open WebUI 中开箱即用。

:::note
存在您的网络搜索被速率限制的可能性。
:::

## Tavily API

Coming Soon

### 设置

## Jina API

Coming Soon

### 设置

## Bing API

### 设置

1. 导航到 [AzurePortal](https://portal.azure.com/#create/Microsoft.BingSearch) 并创建新资源。创建后，您将重定向到资源概览页面。从那里，选择 "Click here to manage keys." ![click here to manage keys](https://github.com/user-attachments/assets/dd2a3c67-d6a7-4198-ba54-67a3c8acff6d)
2. 在密钥管理页面中，找到 Key1 或 Key2 并复制所需的密钥。
3. 打开 Open WebUI Admin Panel，切换到 Settings 选项卡，然后选择 Web Search。
4. 启用 Web search 选项并设置 Web Search Engine 为 bing。
5. 填写 `SearchApi API Key` 并填写从 [AzurePortal](https://portal.azure.com/#create/Microsoft.BingSearch) 仪表板复制的 API key。
6. 单击 `Save`。

