### `uv` 安装指南

`uv` 是一个高性能的 Python 包管理器和运行时管理器，可以为 Open WebUI 这样的应用程序提供高效的 Python 环境管理。请按照以下步骤操作：

#### 1. 安装 `uv`

根据您的操作系统选择对应的安装指令：

- **macOS/Linux**：  
  ```bash
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```

- **Windows**：  
  ```powershell
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```

#### 2. 启动 Open WebUI

完成 `uv` 安装后，启动 Open WebUI 只需一个命令。请注意设置 `DATA_DIR` 环境变量以指定数据存储位置，这对于数据持久化非常重要。以下是各平台的配置示例：

- **macOS/Linux**：  
  ```bash
  DATA_DIR=~/.open-webui uvx --python 3.11 open-webui@latest serve
  ```

- **Windows**：  
  ```powershell
  $env:DATA_DIR="C:\open-webui\data"; uvx --python 3.11 open-webui@latest serve
  ```
