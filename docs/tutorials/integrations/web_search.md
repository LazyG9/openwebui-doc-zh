---
sidebar_position: 5
title: "🌐 网页搜索"
---

:::warning
本教程是社区贡献内容，不受 Open WebUI 团队官方支持。它仅作为如何根据特定需求自定义 Open WebUI 的示例。想要贡献内容？请查看贡献教程。
:::

## 🌐 网页搜索

本指南提供了如何在 Open WebUI 中使用各种搜索引擎设置网页搜索功能的说明。

## SearXNG (Docker)

> "**SearXNG 是一个免费的互联网元搜索引擎（即可以同时搜索多个搜索引擎的搜索引擎），它能够整合多个搜索服务和数据库的结果。用户的隐私得到完全保护，不会被收集任何使用数据。**"

## 一、SearXNG 配置

要为 Open WebUI 优化配置 SearXNG，请按照以下步骤操作：

**第一步: 克隆 SearXNG Docker 项目并进入项目目录**

1. 创建新目录 `searxng-docker`

首先需要克隆（clone）searxng-docker 代码仓库（repository）。这个文件夹将用于存放您的 SearXNG 配置文件。详细的配置说明请参考 [SearXNG 官方文档](https://docs.searxng.org/)。

```bash
# 克隆 SearXNG Docker 项目到本地
git clone https://github.com/searxng/searxng-docker.git
```

进入 `searxng-docker` 项目目录：

```bash
# 切换到项目目录
cd searxng-docker
```

**第二步：找到并修改 `.env` 文件：**

1. 从 `.env` 文件中取消注释 `SEARXNG_HOSTNAME` 并进行相应设置：

```bash
# 默认监听 https://localhost
# 如需更改，请执行以下操作：
# * 取消注释 SEARXNG_HOSTNAME，并将 <host> 替换为您的 SearXNG 主机名
# * 取消注释 LETSENCRYPT_EMAIL，并将 <email> 替换为您的邮箱（用于申请 Let's Encrypt SSL 证书）

SEARXNG_HOSTNAME=localhost:8080/
# LETSENCRYPT_EMAIL=<email>

# 可选配置：
# 如果您运行的是特小型或特大型实例，可能需要调整以下参数：
# - uwsgi 工作进程数量
# - 每个工作进程的线程数
# 说明：增加工作进程数（即进程数）可以同时处理更多搜索请求，但会相应增加资源消耗

# SEARXNG_UWSGI_WORKERS=4
# SEARXNG_UWSGI_THREADS=4
```

**第三步：修改 `docker-compose.yaml` 文件**

3. 通过修改 `docker-compose.yaml` 文件解除 `localhost` 访问限制：

```bash
# 将访问限制从 127.0.0.1 改为允许所有 IP 访问
sed -i "s/127.0.0.1:8080/0.0.0.0:8080/"
```

**第四步：授予必要权限**

4. 在根目录下运行以下命令，授予容器创建新配置文件的权限（a+rwx 表示所有用户都有读、写、执行权限）：

```bash
sudo chmod a+rwx searxng-docker/searxng
```

**第五步：创建非限制性的 `limiter.toml` 文件**

5. 创建一个不限制访问的 `searxng-docker/searxng/limiter.toml` 配置文件：

<details>
<summary>searxng-docker/searxng/limiter.toml</summary>

```bash
# 此配置文件用于更新默认配置
# 详细说明请参考：https://github.com/searxng/searxng/blob/master/searx/botdetection/limiter.toml

[botdetection.ip_limit]
# 在 IP 限制方法中禁用 link_token 功能
link_token = false

[botdetection.ip_lists]
# 禁止访问的 IP 列表（为空表示不限制）
block_ip = []
# 允许访问的 IP 列表（为空表示不限制）
pass_ip = []
```

</details>

**第六步：删除默认的 `settings.yml` 文件**

6. 如果存在默认的 `searxng-docker/searxng/settings.yml` 文件，需要将其删除。该文件会在首次启动 SearXNG 时自动重新生成：

```bash
# 删除默认配置文件（如果存在）
rm searxng-docker/searxng/settings.yml
```

**第七步：创建新的 `settings.yml` 文件**

:::note
重要说明：首次运行时，您必须暂时移除 `docker-compose.yaml` 文件中 `searxng` 服务配置的 `cap_drop: - ALL` 设置。这是因为该设置会禁用所有容器权限，包括创建必要的 `uwsgi.ini` 配置文件所需的权限。等配置文件创建完成后，请务必将 `cap_drop: - ALL` 添加回配置文件，以确保系统安全性。
:::

7. 按以下步骤临时启动容器来生成新的 settings.yml 文件：

```bash
# 执行以下操作：
# 1. 启动容器（docker compose up -d）
# 2. 等待10秒让配置文件生成（sleep 10）
# 3. 关闭容器（docker compose down）
docker compose up -d ; sleep 10 ; docker compose down
```

**第八步：配置支持的格式并更新服务设置**

8. 修改 `searxng-docker/searxng/settings.yml` 文件，添加必要的格式支持：

```bash
# 将支持的格式从仅 HTML 扩展为同时支持 HTML 和 JSON
# JSON 格式支持对于 API 集成是必需的
sed -i 's/formats: \[\"html\"\/]/formats: [\"html\", \"json\"]/' searxng-docker/searxng/settings.yml
```

为了安全性，需要为您的 SearXNG 实例生成一个随机密钥：

```bash
# 使用 openssl 生成32位随机密钥，并将其替换默认的 ultrasecretkey
sed -i "s|ultrasecretkey|$(openssl rand -hex 32)|g" searxng-docker/searxng/settings.yml
```

如果您使用的是 Windows 系统，可以使用以下 PowerShell 脚本来生成安全密钥：

```powershell
# 以下脚本将：
# 1. 创建一个32字节的随机数组
# 2. 使用加密安全的随机数生成器填充数组
# 3. 将字节转换为十六进制字符串
# 4. 更新配置文件中的密钥
$randomBytes = New-Object byte[] 32
(New-Object Security.Cryptography.RNGCryptoServiceProvider).GetBytes($randomBytes)
$secretKey = -join ($randomBytes | ForEach-Object { "{0:x2}" -f $_ })
(Get-Content searxng-docker/searxng/settings.yml) -replace 'ultrasecretkey', $secretKey | Set-Content searxng-docker/searxng/settings.yml
```

确保服务配置中的端口号与之前的设置一致（本例中使用 8080 端口）：

```bash
# 确保配置文件中的端口号为 8080
sed -i 's/port: 8080/port: 8080/' searxng-docker/searxng/settings.yml
```

设置服务的监听地址：

```bash
# 设置服务监听地址
# 使用 127.0.0.1 表示只允许本地访问
# 如需允许远程访问，可以设置为 0.0.0.0
sed -i 's/bind_address: "0.0.0.0"/bind_address: "127.0.0.1"/' searxng-docker/searxng/settings.yml
```

#### 配置文件说明

#### searxng-docker/searxng/settings.yml（配置示例）

默认的 `settings.yml` 文件包含多项引擎设置。以下是该配置文件的主要内容示例及说明：

<details>
<summary>searxng-docker/searxng/settings.yml</summary>

```yaml
# 详细配置说明请参考：https://docs.searxng.org/admin/settings/settings.html#settings-use-default-settings
use_default_settings: true

server:
  # base_url 在环境变量 SEARXNG_BASE_URL 中定义，具体设置请查看 .env 和 docker-compose.yml 文件
  secret_key: "ultrasecretkey"  # 安全考虑：必须修改此默认密钥！
  limiter: true  # 访问限制器（私有实例可以设为 false 禁用）
  image_proxy: true  # 启用图片代理
  port: 8080  # 服务端口
  bind_address: "0.0.0.0"  # 监听地址

ui:
  static_use_hash: true  # 静态资源使用哈希

search:
  safe_search: 0  # 安全搜索级别
  autocomplete: ""  # 自动完成设置
  default_lang: ""  # 默认语言
  formats:  # 支持的响应格式
    - html  # 网页格式
    - json  # API 调用必需的 JSON 格式
  # 如需限制访问格式，请删除对应项，格式名称需使用小写
  # formats: [html, csv, json, rss]
redis:
  # Redis 数据库连接配置，可通过环境变量 ${SEARXNG_REDIS_URL} 覆盖
  # 详细说明：https://docs.searxng.org/admin/settings/settings_redis.html#settings-redis
  url: redis://redis:6379/0  # Redis 服务器地址
```

重要提示：请确保 settings.yml 文件中配置的端口号与 docker-compose.yml 文件中为 SearXNG 设置的端口号完全一致。

</details>

**第九步：更新 `uwsgi.ini` 文件**

9. 请确保您的 `searxng-docker/searxng/uwsgi.ini` 文件包含以下配置：

<details>
<summary>searxng-docker/searxng/uwsgi.ini</summary>

```ini
[uwsgi]
# 指定运行服务的系统用户和用户组
uid = searxng
gid = searxng

# 设置工作进程数量（一般设置为等于 CPU 核心数）
# 默认值：%k（自动获取 CPU 核心数，在 Dockerfile 中定义）
workers = %k

# 每个工作进程的线程数量
# 默认值：4（在 Dockerfile 中定义）
threads = 4

# 设置创建的 Unix 套接字文件权限
chmod-socket = 666

# 服务器配置和性能优化选项
single-interpreter = true  # 单解释器模式
master = true  # 启用主进程
plugin = python3  # 使用 Python3 插件
lazy-apps = true  # 延迟应用加载
enable-threads = 4  # 启用线程支持

# 指定要加载的 Python 模块
module = searx.webapp

# 设置 Python 环境
pythonpath = /usr/local/searxng/  # Python 包路径
chdir = /usr/local/searxng/searx/  # 工作目录

# 进程管理配置
auto-procname = true  # 自动设置进程名

# 日志配置（考虑隐私保护）
disable-logging = true  # 禁用常规请求日志
log-5xx = true  # 仅记录 5xx 错误

# 请求处理配置
buffer-size = 8192  # 请求缓冲区大小（字节）

# HTTP 连接设置
# 禁用长连接以解决已知问题
# 详见：https://github.com/searx/searx-docker/issues/24
add-header = Connection: close

# 静态文件服务配置
static-map = /static=/usr/local/searxng/searx/static  # 静态文件映射
static-expires = /* 86400  # 静态文件缓存时间（24小时）
static-gzip-all = True  # 启用全局 Gzip 压缩
offload-threads = 4  # 静态文件处理线程数
```

</details>

## 二、替代设置方案

如果您不想修改默认配置，也可以采用一个更简单的方法：创建一个空的 `searxng-docker` 文件夹，然后按照以下设置说明进行操作。

### Docker Compose 设置

首先，将以下环境变量添加到您的 Open WebUI 的 `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True  # 启用网页搜索功能
      RAG_WEB_SEARCH_ENGINE: "searxng"  # 设置搜索引擎为 SearXNG
      RAG_WEB_SEARCH_RESULT_COUNT: 3  # 设置每次搜索返回的结果数量
      RAG_WEB_SEARCH_CONCURRENT_REQUESTS: 10  # 设置最大并发请求数量
      SEARXNG_QUERY_URL: "http://searxng:8080/search?q=<query>"  # 设置 SearXNG 的查询地址
```

然后，为 SearXNG 创建一个 `.env` 配置文件：

```
# SearXNG 基础配置
SEARXNG_HOSTNAME=localhost:8080/  # 设置 SearXNG 主机名和端口
```

接下来，在 SearXNG 的 `docker-compose.yaml` 文件中添加以下配置：

```yaml
services:
  searxng:
    container_name: searxng  # 设置容器名称
    image: searxng/searxng:latest  # 使用最新版本的 SearXNG 镜像
    ports:
      - "8080:8080"  # 将容器的 8080 端口映射到主机的 8080 端口
    volumes:
      - ./searxng:/etc/searxng:rw  # 将配置目录挂载到容器中（读写模式）
    env_file:
      - .env  # 指定环境变量配置文件
    restart: unless-stopped  # 容器停止时自动重启（除非手动停止）
    cap_drop:
      - ALL  # 出于安全考虑，移除所有默认权限
    cap_add:  # 仅添加必要的权限
      - CHOWN  # 允许更改文件所有权
      - SETGID  # 允许设置组 ID
      - SETUID  # 允许设置用户 ID
      - DAC_OVERRIDE  # 允许覆盖文件访问控制
    logging:  # 日志配置
      driver: "json-file"  # 使用 JSON 格式存储日志
      options:
        max-size: "1m"  # 单个日志文件最大限制为 1MB
        max-file: "1"  # 最多保留 1 个日志文件
```

完成配置后，您可以使用以下命令启动整个服务栈：

```bash
# 在后台启动所有服务
docker compose up -d
```

:::note
安全提示：首次运行时，您必须暂时从 `docker-compose.yaml` 文件中的 `searxng` 服务配置中删除 `cap_drop: - ALL` 这一行，这样才能成功创建 `/etc/searxng/uwsgi.ini` 文件。这是因为 `cap_drop: - ALL` 会禁用所有容器权限，包括创建配置文件所需的权限。

重要：首次运行并确认配置文件创建成功后，请务必将 `cap_drop: - ALL` 添加回配置文件，这对于确保系统安全性至关重要。
:::

如果您更倾向于使用单个命令启动服务，也可以使用 `docker run` 命令直接运行 SearXNG：

```bash
# 使用单个 docker run 命令启动 SearXNG
# 此命令包含了所有必要的配置选项
docker run --name searxng --env-file .env -v ./searxng:/etc/searxng:rw -p 8080:8080 --restart unless-stopped --cap-drop ALL --cap-add CHOWN --cap-add SETGID --cap-add SETUID --cap-add DAC_OVERRIDE --log-driver json-file --log-opt max-size=1m,max-file=1 searxng/searxng:latest
```

## 三、验证连接

使用以下命令验证您的 Open WebUI 容器实例是否能够正常连接到 SearXNG 服务：

```bash
# 发送测试查询以验证连接性
# 此命令将返回 JSON 格式的搜索结果
docker exec -it open-webui curl http://host.docker.internal:8080/search?q=this+is+a+test+query&format=json
```

## 四、图形界面配置

1. 进入配置界面：依次点击 `管理面板` -> `设置` -> `网页搜索`
2. 找到并开启 `启用网页搜索` 开关
3. 在 `网页搜索引擎` 下拉菜单中选择 `searxng`
4. 根据您的部署方式，将 `SearXNG 查询 URL` 设置为以下选项之一：

* `http://searxng:8080/search?q=<query>` （适用于 Docker 部署：使用容器名称和端口）
* `http://host.docker.internal:8080/search?q=<query>` （适用于 Docker 部署：使用 Docker 内部 DNS 解析）
* `http://<searxng.local>/search?q=<query>` （适用于本地网络：使用本地域名）
* `https://<search.domain.com>/search?q=<query>` （适用于公网访问：使用自定义域名）

**重要提示：URL 中的 `/search?q=<query>` 部分是必需的，请勿修改此部分。**
5. 根据您的需求调整以下参数：
   - `搜索结果数量`：每次搜索返回的结果条数
   - `并发请求数`：同时可以发送的搜索请求数量
6. 完成设置后，点击保存按钮保存更改

![SearXNG 图形界面配置示意图](/img/tutorial_searxng_config.png)

## 五、在聊天中使用网页搜索

要使用网页搜索功能，只需点击消息输入框旁边的 + 号图标即可。

在弹出的选项中，您可以方便地打开或关闭网页搜索功能。

![网页搜索功能开关界面](/img/web_search_toggle.png)

完成以上所有步骤后，您就已经成功配置了 Open WebUI 的 SearXNG 搜索功能，现在可以开始使用 SearXNG 引擎进行网页搜索了。

#### 重要注意事项

- 网页搜索功能需要在每个聊天会话中手动开启或关闭
- 该功能设置仅在当前会话有效，在以下情况下会自动关闭：
  - 刷新页面时
  - 切换到其他聊天会话时

## Google PSE API（可编程搜索引擎）

### 设置步骤

1. 首先访问 Google Developers 网站，打开 [可编程搜索引擎](https://developers.google.com/custom-search) 页面，登录您的 Google 账号或创建新账号
2. 进入[控制面板](https://programmablesearchengine.google.com/controlpanel/all)，点击右上角的 `添加` 按钮
3. 在创建页面中：
   - 输入搜索引擎的名称
   - 根据需要配置其他搜索选项
   - 完成人机验证
   - 点击 `创建` 按钮确认创建
4. 创建完成后，您需要：
   - 生成 `API 密钥`（用于认证）
   - 获取 `搜索引擎 ID`（用于标识您的搜索引擎）
5. 配置 Open WebUI：
   - 打开 `Open WebUI 管理面板`
   - 进入 `设置` 标签
   - 找到并点击 `网页搜索` 选项
6. 在网页搜索设置中：
   - 启用 `网页搜索` 功能
   - 将 `网页搜索引擎` 设置为 `google_pse`
7. 填写认证信息：
   - 在 `Google PSE API 密钥` 字段中填入您的 API 密钥
   - 在 `Google PSE 引擎 ID` 字段中填入搜索引擎 ID
8. 点击 `保存` 按钮保存所有设置

![Open WebUI 管理面板配置界面](/img/tutorial_google_pse1.png)

#### 使用说明

要在聊天中使用搜索功能，您需要：
1. 在消息输入框旁找到加号（`+`）按钮
2. 点击该按钮启用 `网页搜索` 功能
现在您就可以开始使用强大的 Google 搜索功能了 ;-)

![网页搜索功能启用示意图](/img/tutorial_google_pse2.png)

## Brave API

### Docker Compose 设置

要启用 Brave 搜索，请将以下环境变量添加到您的 Open WebUI `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True  # 启用网页搜索功能（必需）
```

## Mojeek 搜索 API

### 设置步骤

1. 首先访问 [Mojeek 搜索 API 页面](https://www.mojeek.com/services/search/web-search-api/) 申请并获取您的 `API 密钥`
2. 获取到 `API 密钥` 后，按以下步骤配置 Open WebUI：
   - 打开 `Open WebUI 管理面板`
   - 点击 `设置` 标签
   - 找到并点击 `网页搜索` 选项
3. 在网页搜索设置中：
   - 启用 `网页搜索` 功能
   - 将 `网页搜索引擎` 设置为 `mojeek`
4. 在 `Mojeek 搜索 API 密钥` 输入框中填入您获取的 API 密钥
5. 点击 `保存` 按钮完成设置

### Docker Compose 设置

如果您使用 Docker Compose 部署，请将以下环境变量添加到您的 Open WebUI `docker-compose.yaml` 文件中：

```yaml
services:
  open-webui:
    environment:
      ENABLE_RAG_WEB_SEARCH: True  # 启用网页搜索功能
      RAG_WEB_SEARCH_ENGINE: "mojeek"  # 将搜索引擎设置为 Mojeek
      BRAVE_SEARCH_API_KEY: "YOUR_MOJEEK_API_KEY"  # 替换为您的 Mojeek API 密钥
```

## SearchApi API 集成

[SearchApi](https://searchapi.io) 是一个提供实时搜索引擎结果的 API 服务集合。它支持所有能够返回 `organic_results`（自然搜索结果，即未经推广的普通搜索结果）的搜索引擎，无论是现有的还是未来新增的引擎。系统默认使用 `google` 作为网页搜索引擎，但您可以根据需要切换到其他搜索引擎，如：
- `bing`（必应搜索）
- `baidu`（百度搜索）
- `google_news`（谷歌新闻）
- `bing_news`（必应新闻）
- `google_scholar`（谷歌学术）
- `google_patents`（谷歌专利）等

### 设置步骤

1. 获取 API 密钥：
   - 访问 [SearchApi 官网](https://searchapi.io)
   - 登录现有账号或创建新账号
2. 复制 API 密钥：
   - 进入 `仪表盘（Dashboard）`
   - 找到并复制您的 API 密钥
3. 配置 Open WebUI：
   - 打开 `Open WebUI 管理面板`
   - 进入 `设置` 标签页
   - 找到并点击 `网页搜索` 选项
4. 启用搜索功能：
   - 开启 `网页搜索` 开关
   - 从下拉菜单中选择 `searchapi` 作为 `网页搜索引擎`
5. 填写 API 密钥：
   - 将第 2 步复制的密钥粘贴到 `SearchApi API 密钥` 输入框中
6. 选择搜索引擎【可选步骤】：
   - 在 `SearchApi 引擎` 字段中输入您想使用的搜索引擎名称
   - 可选的引擎包括：
     * `google`（默认选项，谷歌搜索）
     * `bing`（必应搜索）
     * `baidu`（百度搜索）
     * `google_news`（谷歌新闻）
     * `bing_news`（必应新闻）
     * `google_videos`（谷歌视频）
     * `google_scholar`（谷歌学术）
     * `google_patents`（谷歌专利）
7. 保存设置：
   - 点击 `保存` 按钮完成配置

![Open WebUI 管理面板配置界面](/img/tutorial_searchapi_search.png)

#### 使用说明

要开始使用 SearchApi 进行网页搜索，您需要：
1. 在聊天界面找到消息输入框
2. 点击输入框旁边的加号（`+`）按钮
3. 在弹出的选项中启用 `网页搜索` 功能
4. 现在您就可以开始使用强大的多引擎搜索功能了

![网页搜索功能启用方法](/img/enable_web_search.png)

## Kagi API

功能开发中，敬请期待。

### 设置步骤

## Serpstack API

功能开发中，敬请期待。

### 设置步骤

## Serper API

功能开发中，敬请期待。

### 设置步骤

## Serply API

功能开发中，敬请期待。

### 设置步骤

## DuckDuckGo API

### 设置步骤

好消息！使用 DuckDuckGo API 不需要任何额外设置，它是 Open WebUI 的内置功能。您可以直接开始使用 DuckDuckGo 进行网页搜索。

:::note
重要提示：DuckDuckGo 可能会对搜索频率进行限制。如果您发现搜索无法使用，可能是因为超出了允许的请求次数限制，请稍后再试。
:::

## Tavily API

功能开发中，敬请期待。

### 设置步骤

## Jina API

功能开发中，敬请期待。

### 设置步骤

## Bing API

### 设置步骤

1. 创建 Bing 搜索资源：
   - 访问 [Azure 门户](https://portal.azure.com/#create/Microsoft.BingSearch)
   - 登录您的 Azure 账号
   - 创建新的 Bing 搜索资源
   - 创建完成后，系统会将您重定向到资源概览页面

2. 获取 API 密钥：
   - 在资源概览页面找到并点击"点击此处管理密钥"选项
   ![密钥管理入口](https://github.com/user-attachments/assets/dd2a3c67-d6a7-4198-ba54-67a3c8acff6d)
   - 在密钥管理页面找到 Key1 或 Key2
   - 复制您选择的密钥

3. 配置 Open WebUI：
   - 打开 Open WebUI 管理面板
   - 进入设置标签页
   - 找到并选择网页搜索选项

4. 设置搜索引擎：
   - 启用网页搜索功能
   - 将网页搜索引擎设置为 `bing`

5. 填写认证信息：
   - 找到 `SearchApi API 密钥` 输入框
   - 将从 [Azure 门户](https://portal.azure.com/#create/Microsoft.BingSearch) 复制的 API 密钥粘贴到此处

6. 完成设置：
   - 检查所有配置是否正确
   - 点击 `保存` 按钮保存设置

