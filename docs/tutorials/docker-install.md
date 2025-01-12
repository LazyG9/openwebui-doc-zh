---
sidebar_position: 0
title: 🐳 安装 Docker
---

:::warning
本教程是社区贡献内容，不由 OpenWebUI 团队提供支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 安装 Docker

## Windows 和 Mac 用户

- 从 [Docker 官方网站](https://www.docker.com/products/docker-desktop) 下载 Docker Desktop。
- 按照网站上的安装说明进行操作。
- 安装完成后，**打开 Docker Desktop** 确保它正常运行。

---

## Ubuntu 用户

1. **打开终端。**

2. **设置 Docker 的 apt 仓库：**

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
如果使用 **Ubuntu 衍生版**（如 Linux Mint），请使用 `UBUNTU_CODENAME` 替代 `VERSION_CODENAME`。
:::

3. **安装 Docker Engine：**

   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
   ```

4. **验证 Docker 安装：**

   ```bash
   sudo docker run hello-world
   ```

---

## 其他 Linux 发行版

对于其他 Linux 发行版，请参考 [Docker 官方文档](https://docs.docker.com/engine/install/)。

---

## 安装和验证 Ollama

1. **下载 Ollama**，访问 [https://ollama.com/](https://ollama.com/)。

2. **验证 Ollama 安装：**
   - 打开浏览器并访问：
     [http://127.0.0.1:11434/](http://127.0.0.1:11434/)。
   - 注意：端口可能因安装设置而异。
