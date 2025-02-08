---
sidebar_position: 4000
title: "🪶 Apache Tika 文档提取"
---

:::warning
注意：本教程来自社区贡献，不由 Open WebUI 团队官方支持。它仅用于演示如何根据具体需求自定义 Open WebUI。如果您也想贡献内容，请参考贡献指南。
:::

## 🪶 Apache Tika 文档提取

本文档提供了将 Apache Tika 集成到 Open WebUI 的详细步骤指南。Apache Tika 是一个功能强大的内容分析工具包，能够从超过一千种不同类型的文件中检测和提取元数据与文本内容。通过单一接口即可处理所有这些文件类型，这使得 Tika 在搜索引擎索引、内容分析、文本翻译等领域发挥重要作用。

环境要求
------------

* 已安装 Open WebUI 实例
* 系统已安装 Docker
* 已配置 Open WebUI 的 Docker 网络环境

集成步骤
----------------

### 步骤 1：部署 Apache Tika

您可以选择以下两种方式之一来运行 Apache Tika：

**方式一：使用 Docker Compose**

在 Open WebUI 实例所在目录创建 `docker-compose.yml` 文件，添加如下配置：

```yml
services:
  tika:
    image: apache/tika:latest-full
    container_name: tika
    ports:
      - "9998:9998"
    restart: unless-stopped
```

执行以下命令启动服务：

```bash
docker-compose up -d
```

**方式二：使用 Docker 命令**

或者，您可以直接使用以下 Docker 命令启动 Apache Tika：

```bash
docker run -d --name tika \
  -p 9998:9998 \
  -restart unless-stopped \
  apache/tika:latest-full
```

注意：如果选择使用 Docker 命令方式，且需要与 Open WebUI 实例在同一网络中运行，请记得添加 `--network` 参数。

### 步骤 2：配置 Open WebUI

要将 Apache Tika 设置为 Open WebUI 的文档提取引擎，请按照以下步骤操作：

* 登录 Open WebUI 管理界面
* 进入`管理面板`的设置区域
* 点击`设置`选项
* 选择`文档`标签页
* 在`默认`文档提取引擎下拉列表中选择 `Tika`
* 将文档提取引擎 URL 设置为 `http://tika:9998`
* 保存所有设置

验证部署
=====================================

要确认 Apache Tika 在 Docker 环境中正常运行，请按照以下步骤进行验证：

### 1. 启动服务

首先确保 Apache Tika 容器已启动并运行：

```bash
docker run -p 9998:9998 apache/tika
```

此命令会启动 Apache Tika 服务，并将容器的 9998 端口映射到主机的相同端口。

### 2. 检查服务状态

通过发送 GET 请求检查服务是否正常运行：

```bash
curl -X GET http://localhost:9998/tika
```

如果服务正常，将收到如下响应：

```
这是 Tika 服务器。请使用 PUT 请求发送文件。
```

### 3. 测试文件处理

您可以通过上传文件来测试集成效果。使用以下 curl 命令：

```bash
curl -T test.txt http://localhost:9998/tika
```

请将 `test.txt` 替换为您要测试的实际文件路径。

Apache Tika 将返回该文件的元数据信息和内容类型。

### 自动化验证脚本

如果您想自动化验证过程，可以使用以下 Python 脚本：

```python
import requests

def verify_tika(file_path, tika_url):
    try:
        # 发送文件到 Tika 服务器并验证结果
        response = requests.put(tika_url, files={'file': open(file_path, 'rb')})

        if response.status_code == 200:
            print("✅ Apache Tika 成功处理了文件")
            print("📝 处理结果：")
            print(response.text)
        else:
            print("❌ 文件处理失败")
            print(f"状态码：{response.status_code}")
            print(f"错误信息：{response.text}")
    except Exception as e:
        print(f"❌ 发生错误：{e}")

if __name__ == "__main__":
    file_path = "test.txt"  # 替换为您的测试文件路径
    tika_url = "http://localhost:9998/tika"

    verify_tika(file_path, tika_url)
```

运行脚本前的准备工作：

### 环境准备

* 确保安装了 Python 3.x
* 安装所需的 requests 库：`pip install requests`
* Apache Tika 容器必须处于运行状态
* 准备好测试用的文件，并更新脚本中的文件路径

### 执行验证

1. 将脚本保存为 `verify_tika.py`
2. 打开命令行终端
3. 切换到脚本所在目录
4. 执行命令：`python verify_tika.py`
5. 查看输出结果，确认服务运行状态

注意：如果遇到问题，请检查 Apache Tika 容器状态和网络连接配置。

### 总结

通过上述步骤，您可以全面验证 Apache Tika 在 Docker 环境中的运行状态。您可以通过文件分析、服务状态检查或自动化脚本来进行测试。如果遇到问题，请确保容器正常运行且网络配置正确。

常见问题排查
--------------

* 确认 Apache Tika 服务可以被 Open WebUI 实例正常访问
* 查看 Docker 容器日志，排查可能的错误信息
* 验证 Open WebUI 中的文档提取引擎 URL 配置是否准确

集成优势
----------------------

将 Apache Tika 集成到 Open WebUI 可以带来以下优势：

* **强大的元数据提取能力**：利用 Apache Tika 的专业解析能力，准确提取文件中的各类元数据
* **广泛的格式支持**：支持数千种文件格式，满足企业处理各类文档的需求
* **深度内容分析**：通过 Apache Tika 的高级分析功能，深入挖掘文件中的有价值信息

总结
----------

将 Apache Tika 集成到 Open WebUI 是一个简单直接的过程，可以显著提升系统的文档处理能力。按照本指南的步骤操作，您就能轻松地将 Apache Tika 配置为 Open WebUI 的文档提取引擎。
