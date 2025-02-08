---
sidebar_position: 200
title: "🔒 使用 Nginx 配置 HTTPS"
---

:::warning
本教程来自社区贡献，不受 Open WebUI 团队官方支持。它仅作为如何根据特定用例自定义 Open WebUI 的示例。想要贡献？请查看贡献教程。
:::

# 使用 Nginx 配置 HTTPS

确保用户与 Open WebUI 之间的通信安全非常重要。HTTPS（超文本传输协议安全版）通过加密传输的数据来防止信息被窃听和篡改。通过配置 Nginx 作为反向代理服务器，您可以轻松地为 Open WebUI 应用启用 HTTPS 功能，从而提升系统的安全性和可信度。

本指南提供了两种配置 HTTPS 的方法：

- **自签名证书**：适合开发环境和内部使用场景
- **Let's Encrypt**：适合需要权威 SSL 证书的生产环境

请根据您的具体需求选择合适的方案。

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

import SelfSigned from './tab-nginx/SelfSigned.md';
import LetsEncrypt from './tab-nginx/LetsEncrypt.md';

<Tabs>
  <TabItem value="letsencrypt" label="Let's Encrypt">
    <LetsEncrypt />
  </TabItem>
</Tabs>

## 后续步骤

完成 HTTPS 配置后，您可以通过以下地址安全地访问 Open WebUI：

- [https://localhost](https://localhost)

如果您使用域名访问，请确保已正确配置 DNS 解析记录。对于生产环境，我们强烈建议使用 Let's Encrypt 获取权威的 SSL 证书。

---
