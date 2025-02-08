# Helm 部署指南

Helm 是 Kubernetes 的包管理器，它可以帮助您简化 Kubernetes 应用程序的部署和管理流程。

## 前置要求

- 已配置并正常运行的 Kubernetes 集群
- 已正确安装并配置 Helm 工具
- 具有集群的管理员权限

## 安装步骤

1. **添加 Open WebUI Helm 仓库：**
   
   运行以下命令添加官方仓库并更新本地缓存：
   ```bash
   helm repo add open-webui https://open-webui.github.io/helm-charts
   helm repo update
   ```

2. **安装 Open WebUI Chart：**
   
   执行以下命令部署 Open WebUI：
   ```bash
   helm install openwebui open-webui/open-webui
   ```

3. **验证安装状态：**
   
   检查 Pod 运行状态：
   ```bash
   kubectl get pods
   ```

## 访问服务

要从集群外部访问 Open WebUI，您可以：
- 配置端口转发：使用 `kubectl port-forward` 进行本地访问
- 设置负载均衡器：通过云服务提供商的负载均衡服务暴露服务
- 配置 Ingress：使用 Kubernetes Ingress 控制器管理外部访问
