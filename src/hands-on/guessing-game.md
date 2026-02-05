# 猜数字游戏 (Guessing Game)

这是 Rust 官方教程中的经典案例。我们将用“全能系统化”的视角重新审视它，挖掘其中隐藏的工程细节。

## 1. 需求分析
- 生成一个 1-100 的随机数。
- 读取用户输入。
- 比较大小并给出提示（大了/小了/猜对了）。
- 循环直到猜对。

## 2. 引入依赖 (Crates)

我们需要随机数生成器。Rust 标准库（std）为了保持精简，没有内置随机数功能。我们需要引入 `rand` crate。

修改 `Cargo.toml`:

```toml
[dependencies]
rand = "0.8.5"
```

> **SemVer 哲学**: `0.8.5` 实际上意味着 `^0.8.5`，即允许更新到 `0.8.z` 的任何版本，但不能升级到 `0.9.0`。这是 Rust 社区对语义化版本 (Semantic Versioning) 的严格遵守。

## 3. 完整代码解构

```rust
use std::io; // 引入标准库的 IO 模块
use std::cmp::Ordering; // 引入比较枚举
use rand::Rng; // 引入 Rng trait (只有引入了 trait，才能调用其方法)

fn main() {
    println!("猜数字游戏！");

    // thread_rng 是线程本地的随机数生成器
    // gen_range 是 Rng trait 定义的方法
    let secret_number = rand::thread_rng().gen_range(1..=100);

    loop {
        println!("请输入你的猜测：");

        // 创建一个可变的空字符串
        // String::new() 调用的是关联函数 (Associated Function)
        let mut guess = String::new();

        // 1. read_line(&mut guess): 传入可变引用，让函数修改它
        // 2. expect(...): 处理 Result 枚举。如果是 Err，就崩溃并打印消息
        io::stdin()
            .read_line(&mut guess)
            .expect("读取行失败");

        // Shadowing (遮蔽): 我们定义了一个新的 guess 变量，覆盖了上面的字符串 guess
        // parse(): 将字符串解析为数字。它是泛型的，需要类型标注 (u32) 来推断
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue, // 如果输入的不是数字，跳过本次循环
        };

        // match 表达式：Rust 控制流的核心
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("太小了！"),
            Ordering::Greater => println!("太大了！"),
            Ordering::Equal => {
                println!("猜对了！");
                break; // 退出循环
            }
        }
    }
}
```

## 4. 关键知识点深挖

### Shadowing (变量遮蔽)
```rust
let mut guess = String::new();
let guess: u32 = ...;
```
在其他语言中，这会报错（变量重名）。但在 Rust 中，这是合法的，并且被鼓励。
- **Why?** 它允许我们复用变量名，而不需要想出 `guess_str`, `guess_int` 这种笨拙的名字。它通常用于类型转换场景。

### Result 枚举与错误处理
`read_line` 返回 `io::Result`，这是一个枚举：
- `Ok(T)`: 成功，包含返回值。
- `Err(E)`: 失败，包含错误信息。

如果你不处理这个 Result（比如不调用 `expect` 或 `match`），编译器会发出警告。这是 Rust 强迫你面对可能发生的错误的体现。

### Trait 作用域
注意 `use rand::Rng;` 这一行。
如果我们删掉它，`gen_range` 方法就会报错找不到。
> **RULE**: 要调用一个 Trait 的方法，必须先将该 Trait 引入当前作用域。
