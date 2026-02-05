# 术语字典：类型系统 (Type System)

## 1. Trait

- **全称**: Trait
- **词源**: 英语单词，意为“特征”、“特质”。
- **含义**: 定义了某种类型具有的**行为 (Behavior)**。类似于 Java 中的 Interface 或 C++ 中的 Abstract Class。
- **哲学**:
    - **行为优先**: 我们不关心你“是”什么（继承），我们只关心你“能做”什么（组合）。
    - **Shared Behavior**: 如果多个类型都能像鸭子一样叫，那它们就都实现了 `Quack` trait。

## 2. Impl

- **全称**: Implementation
- **词源**: 动词 Implement (实施/实现) 的缩写。
- **含义**: 为某个类型定义具体的方法，或者实现某个 Trait。
- **哲学**:
    - **数据与行为分离**: 在 C++/Java 中，类的方法通常写在类的定义里。但在 Rust 中，`struct` 只定义数据（字段），`impl` 块定义行为（方法）。这使得代码结构极其清晰，也允许你为别人的类型（甚至标准库类型）添加自己的 Trait 实现（只要遵守孤儿规则）。

## 3. Generic

- **全称**: Generic Programming
- **词源**: General (通用的) -> Generic (泛型)。
- **含义**: 编写不依赖具体类型的代码。
- **哲学**: **单态化 (Monomorphization)**。
    - 这是 Rust 零成本抽象的关键。虽然你写的是泛型代码，但在编译时，Rust 会为你用到的每一个具体类型生成一份专用代码。
    - *结果*：泛型函数的运行速度 = 手写具体类型函数的运行速度。
    - *代价*：编译后的二进制文件体积变大（代码膨胀）。

## 4. Enum

- **全称**: Enumeration
- **词源**: 逐一枚举。
- **含义**: 在 Rust 中，Enum 远超其他语言的“枚举”。它是 **代数数据类型 (Algebraic Data Types, ADT)**。
- **哲学**:
    - `Option<T>` (有些或没有) 和 `Result<T, E>` (成功或失败) 都是 Enum。
    - 它们强迫开发者处理每一种可能性。你不能忽略 `None` 或 `Err`，因为编译器会强制你匹配（Match）所有变体。这是 Rust 消除 `Null Pointer Exception` 的基石。
