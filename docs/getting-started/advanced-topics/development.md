---
sidebar_position: 5
title: "🛠️ 开发指南"
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


欢迎阅读 **Open WebUI 开发环境搭建指南！** 无论你是新手还是经验丰富的开发者，本指南都将帮助你为前端和后端组件搭建**本地开发环境**。让我们开始吧！🚀

## 系统要求

- **操作系统**：Linux（或 Windows 上的 WSL - Windows Subsystem for Linux）或 macOS  
- **Python 版本**：Python 3.11 或更高版本  
- **Node.js 版本**：20.10 或更高版本

## 开发方法

<Tabs groupId="dev-setup">

<TabItem value="local" label="本地搭建">

### 🐧 本地开发环境搭建

1. **克隆代码仓库**：

   ```bash
   # 克隆项目代码并进入项目目录
   git clone https://github.com/open-webui/open-webui.git
   cd open-webui
   ```

2. **前端设置**：
   - 创建环境配置文件：

     ```bash
     # 从示例配置创建实际配置文件
     cp -RPp .env.example .env
     ```

   - 安装项目依赖：

     ```bash
     # 安装所有必需的 npm 包
     npm install
     ```

   - 启动开发服务器：

     ```bash
     # 以开发模式启动前端服务
     npm run dev
     ```

     🌐 访问地址：[http://localhost:5173](http://localhost:5173)。

3. **后端设置**：
   - 进入后端目录：

     ```bash
     # 切换到后端代码目录
     cd backend
     ```

   - 使用 Conda（Python 环境管理工具）设置环境：

     ```bash
     # 创建并激活专用的 Python 环境
     conda create --name open-webui python=3.11
     conda activate open-webui
     ```

   - 安装依赖：

     ```bash
     # 安装并更新所有 Python 依赖包
     pip install -r requirements.txt -U
     ```

   - 启动后端服务：

     ```bash
     # 启动开发模式的后端服务
     sh dev.sh
     ```

     📄 API 文档访问地址：[http://localhost:8080/docs](http://localhost:8080/docs)。

</TabItem>

<TabItem value="docker" label="Docker 搭建">

### 🐳 基于 Docker 的开发环境搭建

1. **创建 Docker Compose 配置文件**：

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
   # 启动所有服务并监视变更
   docker compose -f compose-dev.yaml up --watch
   ```

3. **停止容器**：

   ```bash
   # 停止并移除所有容器
   docker compose -f compose-dev.yaml down
   ```

</TabItem>
<TabItem value="conda" label="可选的 Conda 搭建">

### Conda 环境搭建

如果你更喜欢使用 Conda 进行环境隔离（推荐用于 Python 开发）：

1. **创建并激活环境**：

   ```bash
   # 创建专用的 Python 开发环境
   conda create --name open-webui-dev python=3.11
   conda activate open-webui-dev
   ```

2. **安装依赖**：

   ```bash
   # 安装所需的 Python 包
   pip install -r requirements.txt
   ```

3. **运行服务器**：
   - 前端服务：

     ```bash
     # 启动前端开发服务器
     npm run dev
     ```

   - 后端服务：

     ```bash
     # 启动后端开发服务器
     sh dev.sh
     ```

</TabItem>

<TabItem value="troubleshooting" label="故障排除">

## 🐛 故障排除

### **致命错误：达到堆内存限制**

如果在构建过程中遇到内存相关错误（常见于大型前端项目），请增加 Node.js 堆内存大小：

1. **修改 Dockerfile**：

   ```dockerfile
   # 增加 Node.js 可用内存至 4GB
   ENV NODE_OPTIONS=--max-old-space-size=4096
   ```

2. **为 Node.js 分配至少 4 GB 的系统内存**。

---

### **其他常见问题**

- **端口冲突**：  
   确保没有其他进程占用 8080（后端 API）或 5173（前端开发服务器）端口。可以使用 `lsof -i :端口号` 检查。

- **热重载不工作**：  
   - 确认前端和后端的监视模式是否正确启用
   - 检查文件系统权限
   - 验证 `nodemon` 或 `webpack` 配置是否正确

</TabItem>

</Tabs>

## 为 Open WebUI 贡献代码

### 本地工作流程

1. **定期提交更改**以跟踪开发进度。
2. **与主分支保持同步**以避免代码冲突：

   ```bash
   # 拉取主分支最新代码
   git pull origin main
   ```

3. **推送前运行测试**：

   ```bash
   # 运行所有单元测试和集成测试
   npm run test
   ```

祝编码愉快！🎉
