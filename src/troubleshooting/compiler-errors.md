# 编译器报错哲学 (Compiler Errors Philosophy)

新手往往会被 Rust 冗长的报错信息吓退。但请记住：**Rust 编译器是你最好的结对编程伙伴，而不是你的敌人。**

## 1. 报错信息的结构

Rust 的报错信息经过精心设计，通常包含三个部分：

1.  **Error Code (错误码)**: 如 `E0382`。这是错误的唯一索引。你可以用 `rustc --explain E0382` 查看详细解释。
2.  **Code Snippet (代码片段)**: 带有颜色高亮和 ASCII 艺术箭头的代码引用，精确指出错误发生的位置。
3.  **Help/Suggestion (帮助建议)**: 编译器猜测你想要做什么，并给出修改建议。

## 2. 案例分析

```rust
let x = 5;
x = 6;
```

**报错**:
```text
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:3:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x = 5;
  |         +++
```

**解读**:
- 它不仅告诉你“不能赋值两次”。
- 它还指出了第一次赋值在哪。
- 最重要的是，它给出了 `help`，直接告诉你加个 `mut` 就能解决。

## 3. 驱动式开发 (Compiler-Driven Development)

在 Rust 中，一种高效的开发模式是：
1.  写出你认为逻辑正确的代码（哪怕类型还没对齐）。
2.  运行 `cargo check`。
3.  **只看第一个报错**。
4.  修复它。
5.  重复。

不要试图一次性修复所有报错，因为一个类型错误可能会引发后续一连串的连锁反应报错。修复了源头，后面的往往就自动消失了。
