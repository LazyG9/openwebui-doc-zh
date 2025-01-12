---
sidebar_position: 2
title: "🤝 贡献教程"
---

:::warning
本教程是社区贡献内容，不由 OpenWebUI 团队提供支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::

# 贡献教程

我们感谢您有兴趣为 Open WebUI 文档贡献教程。请按照以下步骤设置您的环境并提交您的教程。

## 步骤

1. **Fork `openwebui/docs` GitHub 仓库**

   - 访问 GitHub 上的 [Open WebUI Docs 仓库](https://github.com/open-webui/docs)。
   - 点击右上角的 **Fork** 按钮，在您的 GitHub 账户下创建一个副本。

2. **启用 GitHub Actions**

   - 在您的 fork 仓库中，导航至 **Actions** 标签页。
   - 如果出现提示，按照屏幕上的说明启用 GitHub Actions。

3. **启用 GitHub Pages**

   - 在您的 fork 仓库中，前往 **Settings** > **Pages**。
   - 在 **Source** 下，选择您要部署的分支（例如 `main`）和文件夹（例如 `/docs`）。
   - 点击 **Save** 启用 GitHub Pages。

4. **配置 GitHub 环境变量**

   - 在您的 fork 仓库中，前往 **Settings** > **Secrets and variables** > **Actions** > **Variables**。
   - 添加以下环境变量：
     - `BASE_URL` 设置为 `/docs`（或您为 fork 选择的基础 URL）。
     - `SITE_URL` 设置为 `https://<your-github-username>.github.io/`。

### 📝 更新 GitHub Pages 工作流和配置文件

如果您需要调整部署设置以适应您的自定义设置，请按以下步骤操作：

a. **更新 `.github/workflows/gh-pages.yml`**

- 如有必要，在构建步骤中添加 `BASE_URL` 和 `SITE_URL` 的环境变量：

     ```yaml
       - name: Build
         env:
           BASE_URL: ${{ vars.BASE_URL }}
           SITE_URL: ${{ vars.SITE_URL }}
         run: npm run build
     ```

b. **修改 `docusaurus.config.ts` 以使用环境变量**

- 更新 `docusaurus.config.ts` 以使用这些环境变量，为本地或直接部署提供默认值：

     ```typescript
     const config: Config = {
       title: "Open WebUI",
       tagline: "ChatGPT-Style WebUI for LLMs (Formerly Ollama WebUI)",
       favicon: "img/favicon.png",
       url: process.env.SITE_URL || "https://openwebui.com",
       baseUrl: process.env.BASE_URL || "/",
       ...
     };
     ```

- 这种设置可以确保 fork 和自定义设置的部署行为一致。

5. **运行 `gh-pages` GitHub 工作流**

   - 在 **Actions** 标签页中，找到 `gh-pages` 工作流。
   - 如有必要手动触发工作流，或根据您的设置它可能会自动运行。

6. **浏览您的 Fork 副本**

   - 访问 `https://<your-github-username>.github.io/<BASE_URL>` 查看您的 fork 文档。

7. **编写您的更改**

   - 在您的 fork 仓库中，导航到适当的目录（例如 `docs/tutorial/`）。
   - 为您的教程创建新的 markdown 文件或编辑现有文件。
   - 确保您的教程包含不受支持的警告横幅。

8. **提交拉取请求**

   - 当您的教程准备就绪时，将更改提交到您的 fork 仓库。
   - 导航到原始的 `open-webui/docs` 仓库。
   - 点击 **New Pull Request** 并选择您的 fork 和分支作为源。
   - 为您的 PR 提供描述性的标题和说明。
   - 提交拉取请求以供审核。

## 重要说明

社区贡献的教程必须包含以下内容：

```
:::warning
本教程是社区贡献内容，不由 OpenWebUI 团队提供支持。它仅作为如何根据您的特定用例自定义 OpenWebUI 的演示。想要贡献？请查看贡献教程。
:::
```

---

:::tip 如何在本地测试 Docusaurus  
您可以使用以下命令在本地测试您的 Docusaurus 网站：

```bash
npm install   # 安装依赖
npm run build # 构建生产版本的网站
```

这将帮助您在部署之前发现任何问题
:::

---
