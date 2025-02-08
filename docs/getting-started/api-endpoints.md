---
sidebar_position: 400
title: "🔗 API 端点"
---

本指南提供了如何有效使用 API 端点的重要信息，帮助您实现使用我们模型时的无缝集成和自动化操作。请注意，这是一个实验性设置，未来可能会进行更新和优化。

## 身份认证

为确保 API 访问安全，需要进行身份认证 🛡️。您可以使用 Bearer Token 机制对 API 请求进行认证。您可以从 Open WebUI 的**设置 > 账户**中获取 API 密钥，或者使用 JWT（JSON Web Token）进行认证。

## 重要 API 端点

### 📜 获取所有模型

- **端点**: `GET /api/models`
- **描述**: 获取通过 Open WebUI 创建或添加的所有模型。
- **示例**:

  ```bash
  # 获取所有可用模型
  curl -H "Authorization: Bearer YOUR_API_KEY" http://localhost:3000/api/models
  ```

### 💬 聊天补全

- **端点**: `POST /api/chat/completions`
- **描述**: 这是一个与 OpenAI API 兼容的聊天补全端点，支持 Open WebUI 上的所有模型，包括 Ollama 模型、OpenAI 模型和 Open WebUI 函数模型。
- **示例**:

  ```bash
  # 发送聊天请求示例
  curl -X POST http://localhost:3000/api/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "llama3.1",
        "messages": [
          {
            "role": "user",
            "content": "Why is the sky blue?"
          }
        ]
      }'
  ```

### 🧩 检索增强生成（Retrieval Augmented Generation，RAG）

检索增强生成（RAG）功能允许您通过整合外部数据源来增强模型响应。以下是通过 API 管理文件和知识库的方法，以及如何在聊天补全中有效使用它们。

#### 上传文件

要在 RAG 响应中使用外部数据，首先需要上传文件。上传文件的内容会自动提取并存储在向量数据库中。

- **端点**: `POST /api/v1/files/`
- **Curl 示例**:

  ```bash
  # 上传文件到系统
  curl -X POST -H "Authorization: Bearer YOUR_API_KEY" -H "Accept: application/json" \
  -F "file=@/path/to/your/file" http://localhost:3000/api/v1/files/
  ```

- **Python 示例**:

  ```python
  import requests
  
  def upload_file(token, file_path):
      """
      上传文件到系统
      :param token: API 访问令牌
      :param file_path: 文件路径
      :return: 上传响应的 JSON 数据
      """
      url = 'http://localhost:3000/api/v1/files/'
      headers = {
          'Authorization': f'Bearer {token}',
          'Accept': 'application/json'
      }
      files = {'file': open(file_path, 'rb')}
      response = requests.post(url, headers=headers, files=files)
      return response.json()
  ```

#### 将文件添加到知识库

上传后，您可以将文件分组到知识库中，或在聊天中单独引用它们。

- **端点**: `POST /api/v1/knowledge/{id}/file/add`
- **Curl 示例**:

  ```bash
  # 将文件添加到指定知识库
  curl -X POST http://localhost:3000/api/v1/knowledge/{knowledge_id}/file/add \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"file_id": "your-file-id-here"}'
  ```

- **Python 示例**:

  ```python
  import requests

  def add_file_to_knowledge(token, knowledge_id, file_id):
      """
      将文件添加到知识库
      :param token: API 访问令牌
      :param knowledge_id: 知识库 ID
      :param file_id: 文件 ID
      :return: 添加操作的响应数据
      """
      url = f'http://localhost:3000/api/v1/knowledge/{knowledge_id}/file/add'
      headers = {
          'Authorization': f'Bearer {token}',
          'Content-Type': 'application/json'
      }
      data = {'file_id': file_id}
      response = requests.post(url, headers=headers, json=data)
      return response.json()
  ```

#### 在聊天补全中使用文件和知识库

您可以在 RAG 查询中引用单个文件或整个知识库，以获得更丰富的响应。

##### 在聊天补全中使用单个文件

当您想要将聊天模型的响应集中在特定文件的内容上时，这种方法很有用。

- **端点**: `POST /api/chat/completions`
- **Curl 示例**:

  ```bash
  # 使用特定文件进行聊天
  curl -X POST http://localhost:3000/api/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "gpt-4-turbo",
        "messages": [
          {"role": "user", "content": "请解释这个文档中的概念。"}
        ],
        "files": [
          {"type": "file", "id": "your-file-id-here"}
        ]
      }'
  ```

- **Python 示例**:

  ```python
  import requests

  def chat_with_file(token, model, query, file_id):
      """
      使用特定文件进行聊天
      :param token: API 访问令牌
      :param model: 模型名称
      :param query: 查询内容
      :param file_id: 文件 ID
      :return: 聊天响应
      """
      url = 'http://localhost:3000/api/chat/completions'
      headers = {
          'Authorization': f'Bearer {token}',
          'Content-Type': 'application/json'
      }
      payload = {
          'model': model,
          'messages': [{'role': 'user', 'content': query}],
          'files': [{'type': 'file', 'id': file_id}]
      }
      response = requests.post(url, headers=headers, json=payload)
      return response.json()
  ```

##### 在聊天补全中使用知识库

当查询可能受益于更广泛的上下文或多个文档时，可以利用知识库来增强响应。

- **端点**: `POST /api/chat/completions`
- **Curl 示例**:

  ```bash
  # 使用知识库进行聊天
  curl -X POST http://localhost:3000/api/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "gpt-4-turbo",
        "messages": [
          {"role": "user", "content": "请分析知识库中涉及的历史观点。"}
        ],
        "files": [
          {"type": "collection", "id": "your-collection-id-here"}
        ]
      }'
  ```

- **Python 示例**:

  ```python
  import requests
  
  def chat_with_collection(token, model, query, collection_id):
      """
      使用知识库进行聊天
      :param token: API 访问令牌
      :param model: 模型名称
      :param query: 查询内容
      :param collection_id: 知识库 ID
      :return: 聊天响应
      """
      url = 'http://localhost:3000/api/chat/completions'
      headers = {
          'Authorization': f'Bearer {token}',
          'Content-Type': 'application/json'
      }
      payload = {
          'model': model,
          'messages': [{'role': 'user', 'content': query}],
          'files': [{'type': 'collection', 'id': collection_id}]
      }
      response = requests.post(url, headers=headers, json=payload)
      return response.json()
  ```

这些方法能够通过上传的文件和精心构建的知识库有效利用外部知识，增强使用 Open WebUI API 的聊天应用程序的功能。无论是单独使用文件还是在知识库中使用，您都可以根据具体需求定制集成方式。

## 使用 Open WebUI 作为统一大语言模型提供者的优势

Open WebUI 提供了众多优势，使其成为开发者和企业的重要工具：

- **统一接口**：通过单一集成平台简化与不同大语言模型的交互。
- **便捷实施**：借助全面的文档和活跃的社区支持，快速开始集成。

## Swagger 文档链接

:::important
请注意：要访问这些服务的 Swagger 文档，您需要将环境变量 `ENV` 设置为 `dev`。如果未进行此配置，文档将无法访问。
:::

访问 Open WebUI 提供的不同服务的详细 API 文档：

| 应用 | 文档路径 |
|-------------|-------------------------|
| 主要应用 | `/docs` |


按照这些指南，您可以快速集成并开始使用 Open WebUI API。如果遇到任何问题或有疑问，欢迎通过我们的 Discord 社区联系我们或查阅常见问题解答。祝您开发顺利！🚀
