# 零成本抽象 (Zero-Cost Abstractions)

这是 Rust 性能媲美 C++ 的基石。

## 1. 定义
Bjarne Stroustrup (C++ 之父) 定义的“零成本抽象”包含两层含义：
1.  **不用的东西，不需要为之付出代价** (What you don't use, you don't pay for)。
2.  **用的东西，你手写也不可能做得更好** (What you use, you couldn't hand code any better)。

## 2. 迭代器 (Iterators) vs 循环 (Loops)

### 高级抽象：迭代器
```rust
let sum: i32 = vec.iter().map(|x| x * 2).sum();
```
这段代码读起来像英语，充满了函数式编程的味道。它会不会比手动 `for` 循环慢？

### 底层实现：循环
```rust
let mut sum = 0;
for x in &vec {
    sum += x * 2;
}
```

### 结论
**它们生成的汇编代码几乎完全一样！**
甚至，在某些情况下，迭代器版本会更快。因为迭代器对其边界有更精确的掌握，编译器可以消除一些不必要的 **边界检查 (Bounds Check)**，并进行更好的 **循环展开 (Loop Unrolling)** 和 **向量化 (SIMD)** 优化。

## 3. 泛型单态化 (Monomorphization)

当你使用泛型函数 `fn print<T>(x: T)` 时，你并没有付出运行时的动态分发开销（像 Java 的 Erasure 或 Python 的 Dynamic Typing）。

Rust 在编译时，会扫描所有调用 `print` 的地方。
- 如果你调用了 `print(1)` (i32)
- 又调用了 `print("hello")` (&str)

编译器会生成两个函数：
- `print_i32(x: i32)`
- `print_str(x: &str)`

这就叫 **单态化**。
- **Cost**: 编译时间增加，二进制体积增加。
- **Benefit**: 运行时性能达到极致（静态函数调用，内联优化）。
