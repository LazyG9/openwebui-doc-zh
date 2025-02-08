# 虚拟环境安装指南

使用 Python 内置的 `venv` 模块创建独立的 Python 环境，这可以避免依赖冲突并确保应用程序的稳定运行。

## 安装步骤

1. **创建虚拟环境：**
   
   使用以下命令创建一个新的虚拟环境：
   ```bash
   python3 -m venv venv
   ```

2. **激活虚拟环境：**
   
   根据您的操作系统选择相应的激活命令：

   - Linux/macOS 系统：
     ```bash
     source venv/bin/activate
     ```

   - Windows 系统：
     ```bash
     venv\Scripts\activate
     ```

3. **安装 Open WebUI：**
   
   在激活的虚拟环境中安装 Open WebUI：
   ```bash
   pip install open-webui
   ```

4. **启动 Open WebUI 服务：**
   
   运行以下命令启动服务：
   ```bash
   open-webui serve
   ```
