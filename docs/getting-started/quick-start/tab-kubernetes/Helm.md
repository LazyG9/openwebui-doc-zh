# Kubernetes 的 Helm 设置

Helm 帮助您管理 Kubernetes 应用程序。

## 前提条件

- 已设置 Kubernetes 集群。
- 已安装 Helm。

## 步骤

1. **添加 Open WebUI Helm 仓库：**

   ```bash
   helm repo add open-webui https://open-webui.github.io/helm-charts
   helm repo update
   ```

2. **安装 Open WebUI Chart：**

   ```bash
   helm install openwebui open-webui/open-webui
   ```

3. **验证安装：**

   ```bash
   kubectl get pods
   ```

## 访问 WebUI

设置端口转发或负载均衡以从集群外部访问 Open WebUI。
