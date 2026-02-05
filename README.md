# Rust Guide: 行业标杆级系统化教程

## 核心理念 (Philosophy)
本项目不仅仅是一份 Rust 语言的说明书，更是一次关于“系统编程哲学”的深度探索。我们致力于解构 Rust 的每一个核心概念，从词源学（Etymology）的角度解释“为什么是这样”，而不仅仅是“怎么做”。

## 项目结构 (Architecture)
本项目采用 [mdBook](https://rust-lang.github.io/mdBook/) 标准目录结构，以确保最佳的阅读体验和自动化部署能力。

```
rust-guide/
├── book.toml               # mdBook 配置文件 (Configuration)
├── .github/
│   └── workflows/          # CI/CD 自动化工作流
│       └── deploy.yml      # GitHub Pages 部署脚本
├── src/                    # 教程源码 (Source Code)
│   ├── SUMMARY.md          # 目录大纲 (Table of Contents)
│   ├── introduction.md     # 前言 (Preface)
│   ├── concepts/           # 核心概念与哲学 (Concepts & Philosophy)
│   ├── dictionary/         # 术语字典 (Terminology Dictionary)
│   ├── hands-on/           # 实战指南 (Hands-on Guide)
│   ├── advanced/           # 进阶优化 (Advanced Topics)
│   ├── troubleshooting/    # 故障排查 (Troubleshooting)
│   └── deployment/         # 部署发布 (Deployment)
└── README.md               # 项目说明 (Project Readme)
```

## 构建与预览 (Build & Preview)
本教程设计为可以通过 `mdBook` 本地构建，也可以通过 GitHub Actions 自动发布。

### 本地预览
```bash
# 安装 mdbook
cargo install mdbook

# 启动本地服务器
mdbook serve --open
```
