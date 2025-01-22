### 使用 `uv` 安装

`uv` 运行时管理器可以帮助您轻松管理 Open WebUI 所需的 Python 环境。具体步骤如下：

#### 1. 安装 `uv`

请根据您的操作系统执行相应的安装命令：

- **macOS/Linux**：
  ```bash
  # 在终端中执行以下命令
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```

- **Windows**：
  ```powershell
  # 在 PowerShell 中执行以下命令
  powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
  ```

#### 2. 运行 Open WebUI

完成 `uv` 安装后，运行 Open WebUI 非常容易。请使用以下命令，注意必须设置 `DATA_DIR` 环境变量以保护您的数据安全。以下是不同平台的示例命令：

- **macOS/Linux**：
  ```bash
  # 设置数据目录并启动服务
  DATA_DIR=~/.open-webui uvx --python 3.11 open-webui@latest serve
  ```

- **Windows**：
  ```powershell
  # 设置数据目录并启动服务
  $env:DATA_DIR="C:\open-webui\data"; uvx --python 3.11 open-webui@latest serve
  ```
