## 更新

要将本地 Docker 安装更新到最新版本，您可以使用 **Watchtower** 或手动更新容器。

### 选项 1：使用 Watchtower

使用 [Watchtower](https://containrrr.dev/watchtower/)，您可以自动化更新过程：

```bash
docker run --rm --volume /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --run-once open-webui
```

_（如果您的容器名称不同，请将 `open-webui` 替换为您的容器名称。）_

### 选项 2：手动更新

1. 停止并删除当前容器：

   ```bash
   docker rm -f open-webui
   ```

2. 拉取最新版本：

   ```bash
   docker pull ghcr.io/open-webui/open-webui:main
   ```

3. 重新启动容器：

   ```bash
   docker run -d -p 3000:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
   ```

这两种方法都可以将您的 Docker 实例更新并运行最新版本。
