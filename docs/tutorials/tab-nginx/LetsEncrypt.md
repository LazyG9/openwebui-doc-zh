### Let's Encrypt 证书配置指南

Let's Encrypt 提供免费的 SSL 证书，这些证书受到主流浏览器的信任，是生产环境的理想选择。

#### 准备工作

- 确保系统已安装 **Certbot**（SSL 证书管理工具）
- 确保已正确配置 DNS 记录，并指向您的服务器 IP

#### 配置步骤

1. **创建所需目录：**

    ```bash
    mkdir -p conf.d ssl
    ```

2. **配置 Nginx：**

    **创建 `conf.d/open-webui.conf` 文件，内容如下：**

    ```nginx
    server {
        listen 80;
        server_name your_domain_or_IP;  # 替换为您的域名

        location / {
            proxy_pass http://host.docker.internal:3000;
    
            # 添加 WebSocket 支持（0.5.0 版本及以上必需）
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";

            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # （可选）关闭代理缓冲机制，优化模型响应的流式传输
            proxy_buffering off;
        }
    }
    ```

3. **配置 Let's Encrypt 自动化脚本：**

    **创建 `enable_letsencrypt.sh` 文件：**

    ```bash
    #!/bin/bash

    # 说明：此脚本用于自动获取和安装 Let's Encrypt SSL 证书

    DOMAIN="your_domain_or_IP"    # 替换为您的域名
    EMAIL="your_email@example.com" # 替换为您的邮箱

    # 检查并安装 Certbot
    if ! command -v certbot &> /dev/null; then
        echo "正在安装 Certbot..."
        sudo apt-get update
        sudo apt-get install -y certbot python3-certbot-nginx
    fi

    # 申请 SSL 证书
    sudo certbot --nginx -d "$DOMAIN" --non-interactive --agree-tos -m "$EMAIL"

    # 重载 Nginx 配置
    sudo systemctl reload nginx

    echo "✅ Let's Encrypt SSL 证书已成功安装，Nginx 配置已更新。"
    ```

    **设置脚本执行权限：**

    ```bash
    chmod +x enable_letsencrypt.sh
    ```

4. **配置 Docker Compose：**

    在 `docker-compose.yml` 中添加以下 Nginx 服务配置：

    ```yaml
    services:
      nginx:
        image: nginx:alpine
        ports:
          - "80:80"    # HTTP 端口
          - "443:443"  # HTTPS 端口
        volumes:
          - ./conf.d:/etc/nginx/conf.d  # 挂载配置目录
          - ./ssl:/etc/nginx/ssl        # 挂载证书目录
        depends_on:
          - open-webui
    ```

5. **启动 Nginx 服务：**

    ```bash
    docker compose up -d nginx
    ```

6. **执行证书配置：**

    运行脚本以自动获取并安装 SSL 证书：

    ```bash
    ./enable_letsencrypt.sh
    ```

#### 访问说明

配置完成后，您可以通过 HTTPS 安全协议访问 Open WebUI：

[https://your_domain_or_IP](https://your_domain_or_IP)

:::note
注意：Let's Encrypt 证书有效期为 90 天，但 Certbot 会自动处理证书的续期，您无需手动操作。
:::
