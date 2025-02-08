## Docker Swarm

这种安装方式需要 Docker Swarms 的相关知识，因为它使用堆栈文件在 Docker Swarm 中将 3 个隔离的容器部署为服务。

它包含了 ChromaDB、Ollama 和 OpenWebUI 的隔离容器。
此外，还包含了预设的[环境变量](/getting-started/env-configuration)以进一步说明配置。

根据您的硬件设置选择适当的命令：

- **准备工作**：

  需要在主机上创建卷的目录，或者您可以指定自定义位置或卷。
  
  当前示例使用了一个隔离的目录 `data`，它位于与 `docker-stack.yaml` 相同的目录中。
  
      - **示例**：
  
        ```bash
        mkdir -p data/open-webui data/chromadb data/ollama
        ```

- **GPU 支持配置**：

  #### Docker-stack.yaml

    ```yaml
    version: '3.9'

    services:
      openWebUI:
        image: ghcr.io/open-webui/open-webui:main
        depends_on:
            - chromadb
            - ollama
        volumes:
          - ./data/open-webui:/app/backend/data
        environment:
          DATA_DIR: /app/backend/data 
          OLLAMA_BASE_URLS: http://ollama:11434
          CHROMA_HTTP_PORT: 8000
          CHROMA_HTTP_HOST: chromadb
          CHROMA_TENANT: default_tenant
          VECTOR_DB: chroma
          WEBUI_NAME: Awesome ChatBot
          CORS_ALLOW_ORIGIN: "*" # 当前默认配置，生产环境部署前需要修改
          RAG_EMBEDDING_ENGINE: ollama
          RAG_EMBEDDING_MODEL: nomic-embed-text-v1.5
          RAG_EMBEDDING_MODEL_TRUST_REMOTE_CODE: "True"
        ports:
          - target: 8080
            published: 8080
            mode: overlay
        deploy:
          replicas: 1
          restart_policy:
            condition: any
            delay: 5s
            max_attempts: 3

      chromadb:
        hostname: chromadb
        image: chromadb/chroma:0.5.15
        volumes:
          - ./data/chromadb:/chroma/chroma
        environment:
          - IS_PERSISTENT=TRUE
          - ALLOW_RESET=TRUE
          - PERSIST_DIRECTORY=/chroma/chroma
        ports: 
          - target: 8000
            published: 8000
            mode: overlay
        deploy:
          replicas: 1
          restart_policy:
            condition: any
            delay: 5s
            max_attempts: 3
        healthcheck: 
          test: ["CMD-SHELL", "curl localhost:8000/api/v1/heartbeat || exit 1"]
          interval: 10s
          retries: 2
          start_period: 5s
          timeout: 10s

      ollama:
        image: ollama/ollama:latest
        hostname: ollama
        ports:
          - target: 11434
            published: 11434
            mode: overlay
        deploy:
          resources:
            reservations:
              generic_resources:
                - discrete_resource_spec:
                    kind: "NVIDIA-GPU"
                    value: 0
          replicas: 1
          restart_policy:
            condition: any
            delay: 5s
            max_attempts: 3
        volumes:
          - ./data/ollama:/root/.ollama

    ```

  - **其他要求**：

      1. 确保启用了 CUDA，请按照您的操作系统和 GPU 说明进行配置。
      2. 启用 Docker GPU 支持，参见 [Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html "访问 Nvidia 网站")
      3. 按照[此指南配置 Docker Swarm 以支持 GPU](https://gist.github.com/tomlankhorst/33da3c4b9edbde5c83fc1244f010815c#configuring-docker-to-work-with-your-gpus)
    - 确保在 `/etc/nvidia-container-runtime/config.toml` 中启用了 _GPU Resource_，并通过取消注释 `swarm-resource = "DOCKER_RESOURCE_GPU"` 来启用 GPU 资源广告。更新配置文件后，必须在每个节点上重启 docker 守护进程。

- **CPU 支持配置**：
  
    修改 `docker-stack.yaml` 中的 Ollama 服务，移除 `generic_resources:` 相关配置：

    ```yaml
        ollama:
      image: ollama/ollama:latest
      hostname: ollama
      ports:
        - target: 11434
          published: 11434
          mode: overlay
      deploy:
        replicas: 1
        restart_policy:
          condition: any
          delay: 5s
          max_attempts: 3
      volumes:
        - ./data/ollama:/root/.ollama
    ```

- **部署 Docker Stack**：
  
  ```bash
  docker stack deploy -c docker-stack.yaml -d super-awesome-ai
  ```
