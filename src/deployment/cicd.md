# CI/CD 自动化部署集成

本教程不仅教你写 Rust，还教你如何像专业团队一样交付文档。我们将使用 **GitHub Actions** 自动将这份 `mdBook` 编译成 HTML 网站，并发布到 **GitHub Pages**。

## 1. 工作流配置深度解析

文件位置：`.github/workflows/deploy.yml`

### 核心配置解读

```yaml
permissions:
  contents: write
```

- **Why?** 默认情况下，GitHub Actions 的令牌（GITHUB_TOKEN）只有读取权限。
- **What?** 我们需要将编译好的 HTML 文件 **git push** 到 `gh-pages` 分支。这本质上是一次“写入”操作。
- **Error Prediction**: 如果不加这一行，你会收到 `403 Forbidden` 错误，提示 Action 无法推送到远程分支。

### 部署步骤拆解

1.  **Checkout**: 拉取源码。
2.  **Install mdBook**: 使用 `peaceiris/actions-mdbook` 动作。它会自动下载最新版的 `mdbook` 二进制文件并添加到 PATH。
3.  **Build**: 运行 `mdbook build`。
    - 输入：`src/` 目录下的 Markdown。
    - 输出：`book/` 目录下的 HTML/CSS/JS。
4.  **Deploy**: 使用 `peaceiris/actions-gh-pages` 动作。
    - **Magic**: 它会把 `book/` 目录的内容打包，强制推送（Force Push）到一个名为 `gh-pages` 的孤儿分支（Orphan Branch）。
    - **Result**: GitHub Pages 服务检测到 `gh-pages` 分支的更新，自动刷新网站。

## 2. 启用 GitHub Pages

即使配置了 Action，你还需要在仓库设置中打开 Pages 功能：

1.  进入 GitHub 仓库页面 -> **Settings**。
2.  点击左侧栏的 **Pages**。
3.  在 **Build and deployment** 下：
    - Source: 选择 **Deploy from a branch**。
    - Branch: 选择 **gh-pages** (注意：这个分支只有在第一次 Action 运行成功后才会出现)。
    - Folder: **/(root)**。

## 3. 验证部署

当你把代码推送到 `main` 或 `master` 分支后：

1.  点击仓库上方的 **Actions** 标签页。
2.  你应该能看到一个名为 "Deploy to GitHub Pages" 的工作流正在运行。
3.  等待变成绿色对号（✅）。
4.  访问 `https://<your-username>.github.io/rust-guide/`。

> **故障排查**:
> 如果样式丢失（CSS 404），请检查 `book.toml` 中的 `site-url` 配置是否正确。它应该是 `/rust-guide/`（注意前后的斜杠）。
