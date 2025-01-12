---
sidebar_position: 5
title: "🛠️ 开发指南"
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


欢迎阅读 **Open WebUI 开发设置指南！** 无论您是新手还是经验丰富的开发者，本指南都将帮助您为前端和后端组件设置**本地开发环境**。让我们开始吧！🚀

## 系统要求

- **操作系统**：Linux（或 Windows 上的 WSL）或 macOS  
- **Python 版本**：Python 3.11+  
- **Node.js 版本**：20.10+

## 开发方法

<Tabs groupId="dev-setup">

<TabItem value="local" label="本地设置">

### 🐧 本地开发设置

1. **克隆仓库**：

   ```bash
   git clone https://github.com/open-webui/open-webui.git
   cd open-webui
   ```

2. **前端设置**：
   - 创建 `.env` 文件：

     ```bash
     cp -RPp .env.example .env
     ```

   - 安装依赖：

     ```bash
     npm install
     ```

   - 启动前端服务器：

     ```bash
     npm run dev
     ```

     🌐 可在以下地址访问：[http://localhost:5173](http://localhost:5173)。

3. **后端设置**：
   - 进入后端目录：

     ```bash
     cd backend
     ```

   - 使用 **Conda** 进行环境设置：

     ```bash
     conda create --name open-webui python=3.11
     conda activate open-webui
     ```

   - 安装依赖：

     ```bash
     pip install -r requirements.txt -U
     ```

   - 启动后端：

     ```bash
     sh dev.sh
     ```

     📄 API 文档可在以下地址访问：[http://localhost:8080/docs](http://localhost:8080/docs)。

</TabItem>

<TabItem value="docker" label="Docker 设置">

### 🐳 基于 Docker 的开发设置

1. **创建 Docker Compose 文件**：

   ```yaml
   name: open-webui-dev

   services:
     frontend:
       build:
         context: .
         target: build
       command: ["npm", "run", "dev"]
       depends_on:
         - backend
       ports:
         - "3000:5173"
       extra_hosts:
         - host.docker.internal:host-gateway
       volumes:
         - ./src:/app/src

     backend:
       build:
         context: .
         target: base
       command: ["bash", "dev.sh"]
       env_file: ".env"
       environment:
         - ENV=dev
         - WEBUI_AUTH=False
       ports:
         - "8080:8080"
       extra_hosts:
         - host.docker.internal:host-gateway
       volumes:
         - ./backend:/app/backend
         - data:/app/backend/data

   volumes:
     data: {}
   ```

2. **启动开发容器**：

   ```bash
   docker compose -f compose-dev.yaml up --watch
   ```

3. **停止容器**：

   ```bash
   docker compose -f compose-dev.yaml down
   ```

</TabItem>
<TabItem value="conda" label="可选的 Conda 设置">

### Conda 环境设置

如果您更喜欢使用 **Conda** 进行隔离：

1. **创建并激活环境**：

   ```bash
   conda create --name open-webui-dev python=3.11
   conda activate open-webui-dev
   ```

2. **安装依赖**：

   ```bash
   pip install -r requirements.txt
   ```

3. **运行服务器**：
   - 前端：

     ```bash
     npm run dev
     ```

   - 后端：

     ```bash
     sh dev.sh
     ```

</TabItem>

<TabItem value="troubleshooting" label="故障排除">

## 🐛 故障排除

### **致命错误：达到堆限制**

如果在构建过程中遇到内存相关错误，请增加 **Node.js 堆大小**：

1. **修改 Dockerfile**：

   ```dockerfile
   ENV NODE_OPTIONS=--max-old-space-size=4096
   ```

2. **为 Node.js 分配至少 4 GB 的 RAM**。

---

### **其他问题**

- **端口冲突**：  
   确保没有其他进程使用**端口 8080 或 5173**。

- **热重载不工作**：  
   验证前端和后端都启用了**监视模式**。

</TabItem>

</Tabs>

## 为 Open WebUI 做贡献

### 本地工作流程

1. **定期提交更改**以跟踪进度。
2. **与主分支同步**以避免冲突：

   ```bash
   git pull origin main
   ```

3. **推送前运行测试**：

   ```bash
   npm run test
   ```

祝编码愉快！🎉
