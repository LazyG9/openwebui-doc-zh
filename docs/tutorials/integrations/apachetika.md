---
sidebar_position: 4000
title: "🪶 Apache Tika 提取"
---

:::warning
本教程来自社区贡献，未经 OpenWebUI 团队官方支持。它仅用于演示如何根据具体需求自定义 OpenWebUI。如果您也想贡献内容，请参考贡献教程。
:::

## 🪶 Apache Tika 提取

本文档提供了将 Apache Tika 与 OpenWebUI 集成的分步指南。Apache Tika 是一个内容分析工具包，可用于检测和提取超过一千种不同文件类型的元数据和文本内容。所有这些文件类型都可以通过统一接口进行解析，这使得 Tika 在搜索引擎索引、内容分析、翻译等方面特别有用。

前提条件
------------

* OpenWebUI 实例
* 系统已安装 Docker
* 已配置 OpenWebUI 的 Docker 网络

集成步骤
----------------

### 步骤 1：创建 Docker Compose 文件或运行 Docker 命令启动 Apache Tika

您可以选择以下两种方式之一来运行 Apache Tika：

**选项 1：使用 Docker Compose**

在 OpenWebUI 实例所在的目录中创建一个名为 `docker-compose.yml` 的新文件，添加以下配置：

```yml
services:
  tika:
    image: apache/tika:latest-full
    container_name: tika
    ports:
      - "9998:9998"
    restart: unless-stopped
```

使用以下命令启动服务：

```bash
# 在后台启动 Docker Compose 服务
docker-compose up -d
```

**选项 2：使用 Docker Run 命令**

或者，您可以使用以下 Docker 命令直接运行 Apache Tika：

```bash
# 启动 Apache Tika 容器
docker run -d --name tika \
  -p 9998:9998 \
  -restart unless-stopped \
  apache/tika:latest-full
```

注意：如果您选择使用 Docker run 命令，且需要将容器运行在与 OpenWebUI 相同的网络中，请记得添加 `--network` 参数。

### 步骤 2：配置 OpenWebUI 使用 Apache Tika

要在 OpenWebUI 中使用 Apache Tika 作为内容提取引擎，请按照以下步骤操作：

* 登录您的 OpenWebUI 实例
* 进入「管理面板」设置菜单
* 点击「设置」
* 切换到「文档」标签页
* 将「默认」内容提取引擎更改为「Tika」
* 将内容提取引擎 URL 设置为 `http://tika:9998`
* 保存更改

在 Docker 中验证 Apache Tika
=====================================

要验证 Apache Tika 在 Docker 环境中是否正常工作，请按照以下步骤操作：

### 1. 启动 Apache Tika Docker 容器

首先，确保 Apache Tika Docker 容器正在运行：

```bash
# 启动 Tika 服务并映射端口
docker run -p 9998:9998 apache/tika
```

此命令会启动 Apache Tika 容器，并将容器的 9998 端口映射到主机的 9998 端口。

### 2. 验证服务器运行状态

通过发送 GET 请求来验证 Apache Tika 服务器是否正在运行：

```bash
# 检查 Tika 服务是否响应
curl -X GET http://localhost:9998/tika
```

如果服务正常运行，将返回以下响应：

```
This is Tika Server. Please PUT
```

### 3. 验证集成

您可以通过发送测试文件来验证集成是否成功：

```bash
# 发送测试文件到 Tika 服务进行分析
curl -T test.txt http://localhost:9998/tika
```

请将 `test.txt` 替换为您本地的测试文件路径。

Apache Tika 将返回该文件的元数据和内容类型信息。

### 使用脚本验证 Apache Tika

如果您想自动化验证过程，可以使用以下 Python 脚本。该脚本会将文件发送到 Apache Tika 并检查响应：

```python
import requests

def verify_tika(file_path, tika_url):
    try:
        # 发送文件到 Apache Tika 并验证输出
        response = requests.put(tika_url, files={'file': open(file_path, 'rb')})

        if response.status_code == 200:
            print("Apache Tika 成功分析了文件。")
            print("Apache Tika 的响应：")
            print(response.text)
        else:
            print("分析文件时出错：")
            print(f"状态码：{response.status_code}")
            print(f"Apache Tika 的响应：{response.text}")
    except Exception as e:
        print(f"发生错误：{e}")

if __name__ == "__main__":
    file_path = "test.txt"  # 替换为您的测试文件路径
    tika_url = "http://localhost:9998/tika"

    verify_tika(file_path, tika_url)
```

运行脚本的准备工作：

### 前提条件

* 安装 Python 3.x
* 安装 requests 库（使用命令：`pip install requests`）
* 确保 Apache Tika Docker 容器正在运行
* 准备好测试文件，并更新脚本中的文件路径

### 运行脚本

1. 将脚本保存为 `verify_tika.py`
2. 打开终端或命令提示符
3. 切换到脚本所在目录
4. 执行命令：`python verify_tika.py`
5. 查看输出结果，确认 Apache Tika 是否正常工作

注意：如果遇到问题，请检查 Apache Tika 容器是否正确运行，以及文件路径是否正确。

### 总结

按照上述步骤，您可以验证 Apache Tika 是否在 Docker 环境中正常工作。您可以通过发送文件分析、验证服务器状态或使用自动化脚本来进行测试。如果遇到问题，请确保所有配置正确，并检查相关日志信息。

故障排除
--------------

* 确保 Apache Tika 服务正常运行，并且可以从 OpenWebUI 实例访问
* 检查 Docker 日志中是否有 Apache Tika 服务相关的错误信息
* 验证 OpenWebUI 中的内容提取引擎 URL 配置是否正确

集成优势
----------------------

将 Apache Tika 与 OpenWebUI 集成可以带来以下优势：

* **强大的元数据提取**：Apache Tika 的高级元数据提取功能可以帮助您准确提取文件中的相关数据
* **多格式支持**：Apache Tika 支持上千种文件格式，是处理多样化文件的理想解决方案
* **深度内容分析**：Apache Tika 的先进内容分析能力可以帮助您挖掘文件中的有价值信息

结论
----------

将 Apache Tika 与 OpenWebUI 集成是一个简单直接的过程，可以显著提升 OpenWebUI 实例的文件处理能力。按照本文档的指引，您可以轻松地将 Apache Tika 配置为 OpenWebUI 的内容提取引擎。
