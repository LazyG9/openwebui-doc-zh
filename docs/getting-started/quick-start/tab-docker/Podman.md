# Podman 部署指南

Podman 是一个无守护进程的容器引擎，专门用于开发、管理和运行 OCI（Open Container Initiative）容器，是 Docker 的优秀替代方案。

## 基础命令

- **启动容器：**
  
  使用以下命令启动 Open WebUI 容器：
  ```bash
  podman run -d --name openwebui -p 3000:8080 ghcr.io/open-webui/open-webui:main
  ```

- **查看运行中的容器：**
  
  使用以下命令查看所有正在运行的容器：
  ```bash
  podman ps
  ```

## Podman 网络设置

如果遇到网络连接问题，您可能需要调整以下网络设置：

```bash
--network=slirp4netns:allow_host_loopback=true
```

有关更多配置说明，请参阅 Podman [官方文档](https://podman.io/)。
