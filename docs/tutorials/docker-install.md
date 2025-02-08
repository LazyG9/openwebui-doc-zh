---
sidebar_position: 4
title: 🐳 安装 Docker
---

:::warning
本教程来自社区贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据特定用例自定义 Open WebUI 的示例。想要贡献？请查看贡献教程。
:::

# 安装 Docker

## Windows 和 Mac 用户

- 从 [Docker 官网](https://www.docker.com/products/docker-desktop) 下载 Docker Desktop
- 按照网站上的安装指引进行操作
- 安装完成后，**启动 Docker Desktop** 以确保其正常运行

---

## Ubuntu 用户

1. **打开终端**

2. **配置 Docker 的 apt 软件源：**

   ```bash
   sudo apt-get update
   sudo apt-get install ca-certificates curl
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
     $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

:::note
如果您使用的是 **Ubuntu 衍生版**（例如 Linux Mint），请使用 `UBUNTU_CODENAME` 代替 `VERSION_CODENAME`。
:::

3. **安装 Docker Engine：**

   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
   ```

4. **验证 Docker 是否安装成功：**

   ```bash
   sudo docker run hello-world
   ```

---

## 其他 Linux 发行版用户

如果您使用其他 Linux 发行版，请参考 [Docker 官方文档](https://docs.docker.com/engine/install/)。

---

## 安装和验证 Ollama

1. **下载 Ollama**
   请访问 [https://ollama.com/](https://ollama.com/) 下载。

2. **验证 Ollama 是否安装成功：**
   - 在浏览器中访问：
     [http://127.0.0.1:11434/](http://127.0.0.1:11434/)
   - 注意：端口号可能因安装配置而异
