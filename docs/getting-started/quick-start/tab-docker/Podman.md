# 使用 Podman

Podman 是一个无守护进程的容器引擎，用于开发、管理和运行 OCI 容器。

## 基本命令

- **运行容器：**

  ```bash
  podman run -d --name openwebui -p 3000:8080 ghcr.io/open-webui/open-webui:main
  ```

- **列出运行中的容器：**

  ```bash
  podman ps
  ```

## Podman 网络配置

如果出现网络问题，您可能需要调整网络设置：

```bash
--network=slirp4netns:allow_host_loopback=true
```

有关高级配置，请参阅 Podman [文档](https://podman.io/)。
