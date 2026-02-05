# Hello World 深度解构 (Deconstructed)

在其他语言中，Hello World 只是一个“能跑就行”的仪式。但在本教程中，我们将把它作为解剖台上的青蛙，彻底拆解 Rust 程序的编译与运行过程。

## 1. 最小化实现

首先，我们不使用 Cargo，而是直接使用编译器 `rustc`，以此来理解工具链的底层逻辑。

创建文件 `main.rs`：

```rust
fn main() {
    println!("Hello, world!");
}
```

### 逐行解析

- **`fn`**: Function（函数）的缩写。
    - *Why?* Rust 崇尚键盘输入的效率，偏好简短的关键字（如 `pub`, `mut`, `impl`）。
- **`main`**: 入口函数。
    - 这里的代码是程序执行的起点。
- **`println!`**: 这**不是**一个函数，而是一个 **宏 (Macro)**。
    - *怎么看出来的？* 那个感叹号 `!`。
    - *Why Macro?* 如果是普通函数，它只能接受固定数量的参数。但 `println!` 需要支持变长参数（如 `println!("{} {}", a, b)`）。在 Rust 中，只有宏能在编译期重写代码来实现这种灵活性。

## 2. 编译过程解密

运行以下命令：

```bash
rustc main.rs
```

这不仅生成了 `main` 可执行文件，背后还发生了惊人的魔法：

1.  **词法分析 (Lexing)**: 将源代码切分成 Token。
2.  **语法分析 (Parsing)**: 生成抽象语法树 (AST)。
3.  **宏展开 (Macro Expansion)**: `println!` 被替换为真正的 I/O 代码（涉及 `std::io::stdio::print`）。
4.  **HIR 生成 (High-Level IR)**: 进行类型检查和借用检查（Borrow Checking）。**这是 Rust 安全性的核心关卡**。
5.  **MIR 生成 (Mid-Level IR)**: 进行基于控制流的优化（如死代码消除）。
6.  **LLVM IR 生成**: 翻译给 LLVM 后端。
7.  **机器码生成**: LLVM 最终吐出针对你 CPU 架构的 0101 二进制码。

## 3. 使用 Cargo (现代方式)

在实际开发中，我们几乎总是使用 Cargo。

```bash
cargo new hello_cargo
cd hello_cargo
cargo run
```

### `cargo run` 到底干了什么？

它是一个组合命令，相当于连续执行了：

1.  **Check**: `cargo check` (检查代码是否能通过编译，但不生成二进制，速度极快)。
2.  **Build**: `cargo build` (真正调用 `rustc` 生成二进制文件，放在 `target/debug/` 下)。
3.  **Execute**: `./target/debug/hello_cargo` (运行生成的文件)。

> **Debug vs Release**:
> 默认情况下，Cargo 进行 **Debug 构建**。
> - 特点：编译快，运行慢，包含调试符号，无优化。
> - 场景：开发阶段。
>
> 当你准备上线时，必须使用 `cargo run --release`。
> - 特点：编译极慢（因为要做大量优化），运行极快（有时比 Debug 快 10-100 倍），去除调试符号。
> - 场景：生产环境。
