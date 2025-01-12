### Let's Encrypt

Let's Encrypt 提供免费的 SSL 证书，受大多数浏览器信任，非常适合生产环境。

#### 前提条件

- 系统已安装 **Certbot**。
- DNS 记录已正确配置并指向您的服务器。

#### 步骤

1. **创建 Nginx 文件目录：**

    ```bash
    mkdir -p conf.d ssl
    ```

2. **创建 Nginx 配置文件：**

    **`conf.d/open-webui.conf`：**

    ```nginx
    server {
        listen 80;
        server_name your_domain_or_IP;

        location / {
            proxy_pass http://host.docker.internal:3000;
    
            # 添加 WebSocket 支持（0.5.0 及以上版本必需）
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";

            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # (可选) 禁用代理缓冲以获得更好的模型流式响应
            proxy_buffering off;
        }
    }
    ```

3. **简化的 Let's Encrypt 脚本：**

    **`enable_letsencrypt.sh`：**

    ```bash
    #!/bin/bash

    # 描述：使用 Certbot 获取和安装 Let's Encrypt SSL 证书的简化脚本。

    DOMAIN="your_domain_or_IP"
    EMAIL="your_email@example.com"

    # 如果未安装 Certbot 则安装
    if ! command -v certbot &> /dev/null; then
        echo "未找到 Certbot。正在安装..."
        sudo apt-get update
        sudo apt-get install -y certbot python3-certbot-nginx
    fi

    # 获取 SSL 证书
    sudo certbot --nginx -d "$DOMAIN" --non-interactive --agree-tos -m "$EMAIL"

    # 重新加载 Nginx 以应用更改
    sudo systemctl reload nginx

    echo "Let's Encrypt SSL 证书已安装，并已重新加载 Nginx。"
    ```

    **使脚本可执行：**

    ```bash
    chmod +x enable_letsencrypt.sh
    ```

4. **更新 Docker Compose 配置：**

    将 Nginx 服务添加到您的 `docker-compose.yml` 中：

    ```yaml
    services:
      nginx:
        image: nginx:alpine
        ports:
          - "80:80"
          - "443:443"
        volumes:
          - ./conf.d:/etc/nginx/conf.d
          - ./ssl:/etc/nginx/ssl
        depends_on:
          - open-webui
    ```

5. **启动 Nginx 服务：**

    ```bash
    docker compose up -d nginx
    ```

6. **运行 Let's Encrypt 脚本：**

    执行脚本以获取和安装 SSL 证书：

    ```bash
    ./enable_letsencrypt.sh
    ```

#### 访问 WebUI

通过 HTTPS 访问 Open WebUI：

[https://your_domain_or_IP](https://your_domain_or_IP)
