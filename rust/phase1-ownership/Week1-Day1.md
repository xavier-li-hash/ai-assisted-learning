# Rust Week 1 - Day 1：环境搭建 + 第一个程序

> 目标：跑通Rust环境，理解Cargo项目结构，感受和Java Maven的区别

---

## Step 1：安装 Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env
rustc --version   # 验证安装
cargo --version
```

---

## Step 2：创建第一个项目

```bash
cargo new hello_rust
cd hello_rust
```

**项目结构（对比Java Maven）：**

| Rust | Java Maven |
|------|-----------|
| `Cargo.toml` | `pom.xml` |
| `src/main.rs` | `src/main/java/.../Main.java` |
| `cargo build` | `mvn compile` |
| `cargo run` | `mvn exec:java` |
| `cargo test` | `mvn test` |

---

## Step 3：今日代码练习

在 `src/main.rs` 里写下面的代码，**不要复制，手敲**：

```rust
fn main() {
    // 变量默认不可变（和Java final一样）
    let x = 5;
    println!("x = {}", x);

    // 可变变量需要显式声明 mut
    let mut y = 10;
    println!("y = {}", y);
    y = 20;  // 如果没有 mut，这行会报错
    println!("y changed to = {}", y);

    // 函数调用
    let result = add(x, y);
    println!("add({}, {}) = {}", x, y, result);
}

fn add(a: i32, b: i32) -> i32 {
    a + b  // 注意：Rust最后一个表达式就是返回值，不需要 return
}
```

运行：
```bash
cargo run
```

---

## Step 4：故意制造错误，读懂编译器

把 `let mut y = 10;` 改成 `let y = 10;`，再次运行：

```bash
cargo run
```

读完整的错误信息。Rust编译器的错误信息非常详细，比Java清晰得多。记录你看到了什么。

**你看到的错误：**
```
（粘贴编译器错误在这里）
```

**你的理解（用自己的话）：**
```
（写下为什么报错，和Java的final有什么区别）
```

---

## Step 5：Java vs Rust 对比思考

| Java | Rust |
|------|------|
| `final int x = 5;` | `let x = 5;` |
| `int y = 10;` | `let mut y = 10;` |
| 默认可变 | **默认不可变** |
| 类型可推断（`var x = 5`）| 类型可推断（`let x = 5`）|

**Java的设计**：变量默认可变，用final约束
**Rust的设计**：变量默认不可变，用mut放开 → 更安全，意图更明确

---

## 今日完成检查

- [ ] Rust环境安装成功（`rustc --version` 有输出）
- [ ] 创建了 `hello_rust` 项目
- [ ] 代码手敲并成功运行
- [ ] 制造了编译错误并读懂了错误信息
- [ ] 能解释 `let` 和 `let mut` 的区别

---

## 明天预告（Day 2）

**所有权三原则**：Rust最核心的概念，Java GC工程师会感到震惊。
