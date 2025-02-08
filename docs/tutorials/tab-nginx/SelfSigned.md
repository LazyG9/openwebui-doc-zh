### 自签名 SSL 证书配置指南

自签名 SSL 证书主要适用于开发测试或内部部署场景，即对安全信任要求不高的环境。

#### 配置步骤

1. **创建必要的目录结构：**

    ```bash
    mkdir -p conf.d ssl
    ```

2. **配置 Nginx：**

    **创建 `conf.d/open-webui.conf` 文件，内容如下：**

    ```nginx
    server {
        listen 443 ssl;
        server_name your_domain_or_IP;  # 替换为您的域名或 IP 地址

        ssl_certificate /etc/nginx/ssl/nginx.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx.key;
        ssl_protocols TLSv1.2 TLSv1.3;  # 仅使用安全的 TLS 版本

        location / {
            proxy_pass http://host.docker.internal:3000;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # （可选）关闭代理缓冲机制，优化模型响应的流式传输
            proxy_buffering off;
        }
    }
    ```

3. **生成自签名 SSL 证书：**

    ```bash
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout ssl/nginx.key \
    -out ssl/nginx.crt \
    -subj "/CN=your_domain_or_IP"  # 替换为您的域名或 IP 地址
    ```

4. **配置 Docker Compose：**

    在 `docker-compose.yml` 中添加以下 Nginx 服务配置：

    ```yaml
    services:
      nginx:
        image: nginx:alpine
        ports:
          - "443:443"  # HTTPS 标准端口
        volumes:
          - ./conf.d:/etc/nginx/conf.d  # 挂载配置目录
          - ./ssl:/etc/nginx/ssl        # 挂载证书目录
        depends_on:
          - open-webui
    ```

5. **启动服务：**

    ```bash
    docker compose up -d nginx
    ```

#### 访问说明

配置完成后，您可以通过 HTTPS 协议访问 Open WebUI：

[https://your_domain_or_IP](https://your_domain_or_IP)

:::note
注意：由于使用的是自签名证书，浏览器可能会显示安全警告。这是正常现象，您可以选择继续访问（仅建议在开发测试环境中这样做）。
:::

---
