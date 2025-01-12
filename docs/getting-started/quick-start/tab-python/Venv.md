# 使用虚拟环境

使用 `venv` 创建隔离的 Python 环境。

## 步骤

1. **创建虚拟环境：**

   ```bash
   python3 -m venv venv
   ```

2. **激活虚拟环境：**

   - 在 Linux/macOS 上：

     ```bash
     source venv/bin/activate
     ```

   - 在 Windows 上：

     ```bash
     venv\Scripts\activate
     ```

3. **安装 Open WebUI：**

   ```bash
   pip install open-webui
   ```

4. **启动服务器：**

   ```bash
   open-webui serve
   ```
