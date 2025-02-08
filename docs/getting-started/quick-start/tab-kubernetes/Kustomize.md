# Kustomize 部署指南

Kustomize 是 Kubernetes 原生的配置管理工具，它允许您以声明式的方式自定义和管理 Kubernetes YAML 配置，无需使用模板。

## 前置要求

- 已配置并正常运行的 Kubernetes 集群
- 已正确安装并配置 Kustomize 工具（kubectl 1.14+ 已内置）
- 具有集群的管理员权限

## 安装步骤

1. **获取 Open WebUI 配置清单：**
   
   克隆官方配置仓库并进入目录：
   ```bash
   git clone https://github.com/open-webui/k8s-manifests.git
   cd k8s-manifests
   ```

2. **部署应用：**
   
   使用 Kustomize 应用配置清单：
   ```bash
   kubectl apply -k .
   ```

3. **验证部署状态：**
   
   检查 Pod 运行状态：
   ```bash
   kubectl get pods
   ```

## 访问服务

要从集群外部访问 Open WebUI，您可以选择以下方式：
- 配置端口转发：使用 `kubectl port-forward` 进行本地访问
- 设置负载均衡器：通过云服务提供商的负载均衡服务暴露服务
- 配置 Ingress：使用 Kubernetes Ingress 控制器管理外部访问
