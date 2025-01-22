---
sidebar_position: 5
title: "🛠️ 开发指南"
---

欢迎使用 **Open WebUI 开发指南**！本文将帮助所有开发者（无论新手还是专家）搭建一个完整的**本地开发环境**。让我们立即开始吧！🚀

## 环境准备

- **系统环境**：Linux（Windows 用户请使用 WSL）或 macOS
- **Python 环境**：3.11 或更高版本
- **Node.js 环境**：22.10 或更高版本

## 开发环境搭建

### 🐧 本地开发环境

1. **获取源码**：

   ```bash
   git clone https://github.com/open-webui/open-webui.git
   cd open-webui
   ```

2. **前端配置**：
    - 配置环境变量：

      ```bash
      cp -RPp .env.example .env
      ```

    - 安装项目依赖：

      ```bash
      npm install
      ```

    - 启动开发服务器：

      ```bash
      npm run dev
      ```

      🌐 前端页面：[http://localhost:5173](http://localhost:5173)

3. **后端配置**：
    - 切换到后端目录：

      ```bash
      cd backend
      ```

    - 创建并激活 **Conda** 环境：

      ```bash
      conda create --name open-webui python=3.11
      conda activate open-webui
      ```

    - 安装项目依赖：

      ```bash
      pip install -r requirements.txt -U
      ```

    - 启动开发服务器：

      ```bash
      sh dev.sh
      ```

      📄 接口文档：[http://localhost:8080/docs](http://localhost:8080/docs)


## 🐛 疑难解答

### **内存溢出问题**

遇到构建时内存不足，请增加 **Node.js 内存限制**：

1. **更新 Dockerfile**：

   ```dockerfile
   ENV NODE_OPTIONS=--max-old-space-size=4096
   ```

2. **确保分配足够内存**（至少 4GB）。

---

### **常见问题**

- **端口被占用**：  
  检查 **8080** 和 **5173** 端口是否被其他程序占用。

- **自动刷新失效**：  
  检查前后端的文件监听是否正常开启。

## 参与项目开发

### 开发工作流

1. **及时提交代码**，保持良好的开发习惯。
2. **保持代码同步**，避免合并冲突：

   ```bash
   git pull origin main
   ```

3. **提交前自测**：

   ```bash
   npm run test
   ```

开始愉快地开发吧！🎉
