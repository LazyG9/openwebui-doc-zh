---
title: "🔒 使用 Nginx 配置 HTTPS"
---

:::warning
本教程是社区贡献内容，不受 OpenWebUI 团队支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 使用 Nginx 配置 HTTPS

确保用户与 Open WebUI 之间的安全通信至关重要。HTTPS（超文本传输协议安全）对传输的数据进行加密，保护其免受窃听和篡改。通过将 Nginx 配置为反向代理，您可以无缝地为 Open WebUI 部署添加 HTTPS，从而提高安全性和可信度。

本指南提供了两种设置 HTTPS 的方法：

- **自签名证书**：适用于开发和内部使用。
- **Let's Encrypt**：适用于需要受信任 SSL 证书的生产环境。

选择最适合您部署需求的方法。

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

import SelfSigned from './tab-nginx/SelfSigned.md';
import LetsEncrypt from './tab-nginx/LetsEncrypt.md';

<Tabs>
  <TabItem value="self-signed" label="自签名证书">
    <SelfSigned />
  </TabItem>

  <TabItem value="letsencrypt" label="Let's Encrypt">
    <LetsEncrypt />
  </TabItem>
</Tabs>

## 后续步骤

设置 HTTPS 后，可以通过以下地址安全地访问 Open WebUI：

- [https://localhost](https://localhost)

如果您使用域名，请确保正确配置 DNS 记录。对于生产环境，建议使用 Let's Encrypt 获取受信任的 SSL 证书。

---
