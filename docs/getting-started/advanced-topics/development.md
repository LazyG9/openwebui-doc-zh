---
sidebar_position: 5
title: "🛠️ 开发指南"
---

欢迎阅读 **Open WebUI 开发环境配置指南！** 无论您是新手还是经验丰富的开发者，本指南都将帮助您为前端和后端组件搭建**本地开发环境**。让我们深入了解吧！🚀

## 系统要求

- **操作系统**：Linux（或 Windows 上的 WSL）或 macOS  
- **Python 版本**：Python 3.11+  
- **Node.js 版本**：22.10+

## 开发方法

### 🐧 本地开发环境配置

1. **克隆（Clone）仓库**：

   ```bash
   git clone https://github.com/open-webui/open-webui.git
   cd open-webui
   ```

2. **前端配置**：
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

     🌐 访问地址：[http://localhost:5173](http://localhost:5173)。

3. **后端配置**：
   - 进入后端目录：

     ```bash
     cd backend
     ```

   - 使用 **Conda** 配置环境：

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

     📄 API 文档访问地址：[http://localhost:8080/docs](http://localhost:8080/docs)。


## 🐛 问题排查

### **致命错误：达到堆内存限制**

如果在构建过程中遇到内存相关错误，请增加 **Node.js 堆内存大小**：

1. **修改 Dockerfile**：

   ```dockerfile
   ENV NODE_OPTIONS=--max-old-space-size=4096
   ```

2. **为 Node.js 分配至少 4 GB 的 RAM**。

---

### **其他问题**

- **端口冲突**：  
   确保没有其他进程占用 **8080 或 5173 端口**。

- **热重载不工作**：  
   验证前端和后端的**监视模式**是否已启用。

## 为 Open WebUI 贡献代码

### 本地工作流程

1. **定期提交代码变更**以跟踪进度。
2. **与主分支保持同步**以避免冲突：

   ```bash
   git pull origin main
   ```

3. **推送前运行测试**：

   ```bash
   npm run test
   ```

开始愉快的编码吧！🎉
