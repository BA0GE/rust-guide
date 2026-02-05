# 构建优化 (Build Optimization)

Rust 的编译速度慢是出了名的。但在 CI/CD 或部署阶段，我们更关心生成产物的体积和运行速度。

## 1. 减小二进制体积

默认的 release 构建虽然去除了符号，但可能还是很大。可以在 `Cargo.toml` 中开启更激进的优化。

```toml
[profile.release]
opt-level = "z"     # 优化体积 (s 是兼顾速度和体积，z 是极致体积)
lto = true          # 链接时优化 (Link Time Optimization)，消除死代码
codegen-units = 1   # 降低并行度，提高优化质量（牺牲编译速度）
panic = "abort"     # 发生 panic 时直接终止，不进行栈展开 (Unwinding)
strip = true        # 自动剥离符号表 (需要 Rust 1.59+)
```

## 2. 缓存策略 (sccache)

在 CI 环境中，每次都重新编译依赖是非常浪费的。
可以使用 `sccache` (Shared Compilation Cache)。它可以缓存编译产物，甚至跨多次构建复用。

## 3. 静态链接 (Static Linking)

Rust 默认是静态链接标准库的。这意味着你编译出的二进制文件扔到另一台同样架构的 Linux 机器上，通常直接能跑，不需要安装 Runtime。

但是，它通常动态链接 `libc`。
如果你想构建一个完全静态的二进制（比如跑在 `scratch` 基础镜像的 Docker 里），需要使用 `musl` 工具链。

```bash
rustup target add x86_64-unknown-linux-musl
cargo build --release --target x86_64-unknown-linux-musl
```
