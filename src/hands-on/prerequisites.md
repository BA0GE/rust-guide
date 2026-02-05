# 环境准备 (Prerequisites)

在开始 Rust 之旅前，我们需要配置一个专业的开发环境。

## 1. 安装 Rust 工具链

### Unix-like (macOS / Linux)
这是官方推荐的方式，使用 `rustup` 安装脚本：

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### Windows
下载并运行 `rustup-init.exe`。

### 验证安装
打开新终端，输入：
```bash
rustc --version
cargo --version
```

## 2. 什么是 Rustup？
不要混淆 `rustc` 和 `rustup`。

- **rustc**: 编译器。
- **cargo**: 包管理器。
- **rustup**: **工具链管理器 (Toolchain Multiplexer)**。
    - 它可以帮你安装不同版本的 rustc (stable, beta, nightly)。
    - 它可以帮你安装交叉编译的目标 (Targets)，比如 `wasm32-unknown-unknown`。
    - 它可以帮你管理辅助组件，如 `rust-analyzer`, `clippy`。

## 3. IDE 选择

### VS Code (推荐)
目前体验最好的轻量级方案。
- **插件**: 必须安装 **rust-analyzer**。
- **注意**: 不要安装官方旧版的 "Rust" 插件，它已经停止维护。请认准 "rust-analyzer"。

### JetBrains RustRover / IntelliJ IDEA
如果你习惯 JetBrains 全家桶，这是一个强大的选择。它有独立的语言分析引擎，不依赖 rust-analyzer。

## 4. 配置国内源 (Optional)
如果你在中国大陆，连接 crates.io 可能会很慢。可以配置字节跳动或清华大学的镜像源。

创建 `~/.cargo/config.toml`:

```toml
[source.crates-io]
replace-with = 'rsproxy'

[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"

[registries.rsproxy]
index = "https://rsproxy.cn/crates.io-index"

[net]
git-fetch-with-cli = true
```
