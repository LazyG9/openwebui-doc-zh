## 版本更新

要将本地 Docker 部署更新到最新版本，您可以使用 **Watchtower** 或手动升级容器。

### 选项 1：使用 Watchtower

使用 [Watchtower](https://containrrr.dev/watchtower/)，您可以自动化更新操作：

```bash
docker run --rm --volume /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --run-once open-webui
```

_（如果您的容器名称与示例不同，请将 `open-webui` 替换为您的实际容器名称。）_

### 选项 2：手动升级

1. 停止并移除当前容器：

   ```bash
   docker rm -f open-webui
   ```

2. 拉取最新镜像：

   ```bash
   docker pull ghcr.io/open-webui/open-webui:main
   ```

3. 重新创建并启动容器：

   ```bash
   docker run -d -p 3000:8080 -v open-webui:/app/backend/data --name open-webui ghcr.io/open-webui/open-webui:main
   ```

以上两种方法均可将您的 Docker 实例更新至最新版本并确保其正常运行。
