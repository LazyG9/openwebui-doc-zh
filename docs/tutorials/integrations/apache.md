---
sidebar_position: 7
title: "🗄️ 分离部署 UI 和模型"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

:::note
如果您计划将其暴露在广域网中，请考虑实施安全措施，如[网络防火墙](https://github.com/chr0mag/geoipsets)、[Web 应用防火墙](https://github.com/owasp-modsecurity/ModSecurity)和[威胁情报](https://github.com/crowdsecurity/crowdsec)。
此外，强烈建议在您的 **HTTPS** 配置中启用 HSTS，可以使用类似 `Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"` 的配置，并在您的 **HTTP** 配置中添加某种形式的重定向到您的 **HTTPS URL**。对于免费的 SSL 证书，[Let's Encrypt](https://letsencrypt.org/) 是一个不错的选择，可以配合 [Certbot](https://github.com/certbot/certbot) 进行管理。
:::

有时，将 Ollama 与 UI 分开托管是有益的，同时保留跨用户共享的 RAG 和 RBAC 支持功能：

## UI 配置

对于 UI 配置，您可以按如下方式设置 Apache VirtualHost：

```
# 假设您在 "server.com" 上托管此 UI
<VirtualHost 192.168.1.100:80>
    ServerName server.com
    DocumentRoot /home/server/public_html

    ProxyPass / http://server.com:3000/ nocanon
    ProxyPassReverse / http://server.com:3000/

    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://server.com:3000/$1" [P,L]
</VirtualHost>
```

在请求 SSL 之前，需要先启用站点：

:::warning
使用 `nocanon` 选项可能会[影响您的后端安全性](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#proxypass)。建议仅在配置需要时启用此选项。
_通常，mod_proxy 会规范化 ProxyPassed URL。但这可能与某些后端不兼容，特别是那些使用 PATH_INFO 的后端。可选的 nocanon 关键字会禁用这种规范化，并将 URL 路径"原样"传递给后端。请注意，此关键字可能会影响后端的安全性，因为它移除了代理提供的针对基于 URL 攻击的常规有限保护。_
:::

`a2ensite server.com.conf` # 这将启用站点。a2ensite 是 "Apache 2 Enable Site" 的缩写

```
# SSL 配置
<VirtualHost 192.168.1.100:443>
    ServerName server.com
    DocumentRoot /home/server/public_html

    ProxyPass / http://server.com:3000/ nocanon
    ProxyPassReverse / http://server.com:3000/

    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://server.com:3000/$1" [P,L]

    SSLEngine on
    SSLCertificateFile /etc/ssl/virtualmin/170514456861234/ssl.cert
    SSLCertificateKeyFile /etc/ssl/virtualmin/170514456861234/ssl.key
    SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1

    SSLProxyEngine on
    SSLCACertificateFile /etc/ssl/virtualmin/170514456865864/ssl.ca
</VirtualHost>
```

我在这里使用 virtualmin 来管理我的 SSL 集群，但您也可以直接使用 certbot 或您喜欢的 SSL 方法。要使用 SSL：

### 先决条件

运行以下命令：

`snap install certbot --classic`
`snap apt install python3-certbot-apache` (这将安装 apache 插件)。

导航到 apache sites-available 目录：

`cd /etc/apache2/sites-available/`

如果尚未创建 server.com.conf，请创建它，包含上述 `<virtualhost>` 配置（应该与您的情况匹配。根据需要修改）。使用不带 SSL 的配置：

创建完成后，运行 `certbot --apache -d server.com`，这将为您请求并添加/创建 SSL 密钥，并创建 server.com.le-ssl.conf

# 配置 Ollama 服务器

在您最新安装的 Ollama 上，确保您已按照官方 Ollama 参考文档设置了 API 服务器：

[Ollama FAQ](https://github.com/jmorganca/ollama/blob/main/docs/faq.md)

### 简要说明

该指南似乎与 Linux 上当前更新的服务文件不匹配。所以，我们将在这里解决这个问题：

除非您是从源代码编译 Ollama，否则使用标准安装 `curl https://ollama.com/install.sh | sh` 会在 /etc/systemd/system 中创建一个名为 `ollama.service` 的文件。您可以使用 nano 编辑该文件：

```
sudo nano /etc/systemd/system/ollama.service
```

Add the following lines:

```
Environment="OLLAMA_HOST=0.0.0.0:11434" # this line is mandatory. You can also specify
```

For instance:

```
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
Environment="OLLAMA_HOST=0.0.0.0:11434" # this line is mandatory. You can also specify 192.168.254.109:DIFFERENT_PORT, format
Environment="OLLAMA_ORIGINS=http://192.168.254.106:11434,https://models.server.city" # this line is optional
User=ollama
Group=ollama
Restart=always
RestartSec=3
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/s>

[Install]
WantedBy=default.target
```

Save the file by pressing CTRL+S, then press CTRL+X

When your computer restarts, the Ollama server will now be listening on the IP:PORT you specified, in this case 0.0.0.0:11434, or 192.168.254.106:11434 (whatever your local IP address is). Make sure that your router is correctly configured to serve pages from that local IP by forwarding 11434 to your local IP server.

# Ollama Model Configuration

## For the Ollama model configuration, use the following Apache VirtualHost setup

Navigate to the apache sites-available directory:

`cd /etc/apache2/sites-available/`

`nano models.server.city.conf` # match this with your ollama server domain

Add the follwoing virtualhost containing this example (modify as needed):

```
# 假设您在 "models.server.city" 上托管此 UI
<IfModule mod_ssl.c>
    <VirtualHost 192.168.254.109:443>
        DocumentRoot "/var/www/html/"
        ServerName models.server.city
        <Directory "/var/www/html/">
            Options None
            Require all granted
        </Directory>

        ProxyRequests Off
        ProxyPreserveHost On
        ProxyAddHeaders On
        SSLProxyEngine on

        ProxyPass / http://server.city:1000/ nocanon # 或端口 11434
        ProxyPassReverse / http://server.city:1000/ # 或端口 11434

        SSLCertificateFile /etc/letsencrypt/live/models.server.city/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/models.server.city/privkey.pem
        Include /etc/letsencrypt/options-ssl-apache.conf
    </VirtualHost>
</IfModule>
```

在请求 SSL 之前，您可能需要先启用站点（如果您尚未这样做）：

`a2ensite models.server.city.conf`

#### Ollama 服务器的 SSL 部分

运行以下命令：

导航到 apache sites-available 目录：

`cd /etc/apache2/sites-available/`
`certbot --apache -d server.com`

```
<VirtualHost 192.168.254.109:80>
    DocumentRoot "/var/www/html/"
    ServerName models.server.city
    <Directory "/var/www/html/">
        Options None
        Require all granted
    </Directory>

    ProxyRequests Off
    ProxyPreserveHost On
    ProxyAddHeaders On
    SSLProxyEngine on

    ProxyPass / http://server.city:1000/ nocanon # 或端口 11434
    ProxyPassReverse / http://server.city:1000/ # 或端口 11434

    RewriteEngine on
    RewriteCond %{SERVER_NAME} =models.server.city
    RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>
```

不要忘记使用 `systemctl reload apache2` 重启/重载 Apache

在 https://server.com 打开您的站点！

**恭喜**，您的 _**类似 Open-AI 的 Chat-GPT 风格 UI**_ 现在正在提供具有 RAG、RBAC 和多模态功能的 AI 服务！如果您还没有下载 Ollama 模型，请立即下载！

如果您遇到任何配置错误或问题，请提交问题或参与我们的讨论。这里有很多友好的开发者可以帮助您。

让我们一起让这个 UI 对每个人都更加友好！

感谢您选择 open-webui 作为您的 AI UI！

本文档由来自菲律宾的 **Open-WebUI** 粉丝 **Bob Reyes** 编写。
