# 常见借用检查错误 (Common Borrow Checker Errors)

Rust 的借用检查器 (Borrow Checker) 是新手最大的噩梦。它像一个严格的图书管理员，拒绝任何可能导致混乱的借书请求。

不要害怕报错，**报错是编译器在教你写出安全的代码**。

## 场景 1: 借出后修改 (Mutation after Borrow)

### 错误代码

```rust
fn main() {
    let mut s = String::from("hello");
    
    let r1 = &s; // 不可变借用 (Immutable Borrow)
    s.push_str(", world"); // 尝试修改原数据 (Mutation)
    
    println!("{}", r1); // 还要继续使用之前的借用
}
```

### 报错分析

```text
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src/main.rs:5:5
  |
4 |     let r1 = &s;
  |              -- immutable borrow occurs here (r1 借走了)
5 |     s.push_str(", world");
  |     ^^^^^^^^^^^^^^^^^^^^^ mutable borrow occurs here (s 想要修改)
6 |     
7 |     println!("{}", r1);
  |                    -- immutable borrow later used here (r1 后面还要用)
```

### 修复逻辑
Rust 禁止在**借出引用**的期间修改原数据。因为如果 `s` 变了（比如扩容导致内存地址变了），`r1` 这个引用就会变成悬空指针。

**解决方案**：确保修改发生在引用的作用域结束之后。

```rust
fn main() {
    let mut s = String::from("hello");
    
    {
        let r1 = &s; 
        println!("{}", r1); 
    } // r1 在这里离开作用域，归还了所有权
    
    s.push_str(", world"); // 现在可以修改了，因为没人借着
}
```

## 场景 2: 悬空引用 (Dangling References)

### 错误代码

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");
    &s // 试图返回 s 的引用
}
```

### 报错分析

```text
error[E0106]: missing lifetime specifier
  |
5 | fn dangle() -> &String {
  |                ^ expected named lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is no value for it to be borrowed from
```

### 修复逻辑
函数 `dangle` 结束时，局部变量 `s` 会被释放（Drop）。如果你返回它的引用，这个引用指向的就是一块“被释放的内存”。Rust 坚决禁止这种行为。

**解决方案**：直接返回所有权（Move），而不是引用。

```rust
fn dangle() -> String { // 去掉 &
    let s = String::from("hello");
    s // 把所有权移交给调用者
}
```

## 总结：借用检查器的三定律

1.  **排他性**：你要么有无数个读者（`&T`），要么有一个作者（`&mut T`），决不能同时存在。
2.  **有效性**：引用必须总是指向有效的内存（引用不能活得比数据更久）。
3.  **显式性**：所有的借用关系必须在编译期就能被推导出来（否则需要生命周期标注）。
