# Rust Week 1 - Day 2：所有权三原则 + Move语义

> 这是Rust最核心的概念。Java工程师最大的心智障碍就在这里。
> 目标：理解为什么Rust不需要GC，以及代价是什么。

---

## 背景：Java vs Rust 的内存哲学

**Java**：
```java
String s1 = "hello";
String s2 = s1;  // s1和s2都指向同一个对象（引用复制）
System.out.println(s1);  // 没问题，s1还能用
System.out.println(s2);  // 没问题
// GC负责在两个引用都消失后回收内存
```

**Rust**：所有权系统让编译器在编译期追踪"谁拥有这块内存"，不需要GC。

---

## 所有权三原则

```
1. Rust中每个值都有一个【所有者（owner）】
2. 同一时刻，每个值只能有【一个】所有者
3. 当所有者离开作用域，这个值被【自动释放（drop）】
```

---

## 今日代码实验（手敲，不要复制）

### 实验1：Move语义（原则2的体现）

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;  // s1的所有权移动给了s2

    // 下面这行会报错！取消注释看看：
    // println!("{}", s1);  // s1已经被"moved"，不能再用

    println!("{}", s2);  // 只有s2可以用
}
```

**理解**：和Java不同，这里s1不是"复制引用"，而是"转移所有权"。s1从此失效。

---

### 实验2：Clone（显式复制，有代价）

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();  // 深拷贝，两个都有效，但有内存开销

    println!("s1 = {}", s1);
    println!("s2 = {}", s2);
}
```

**类比Java**：类似 `new String(s1)` 或深拷贝对象。

---

### 实验3：Copy类型（基础类型不需要Clone）

```rust
fn main() {
    let x = 5;     // i32 实现了 Copy trait
    let y = x;     // x被"复制"，不是移动

    println!("x = {}", x);  // 没问题！
    println!("y = {}", y);
}
```

**规律**：
- 栈上固定大小的类型（i32, f64, bool, char）→ Copy（自动复制）
- 堆上数据（String, Vec, HashMap）→ Move（所有权转移）

---

### 实验4：函数调用中的所有权

```rust
fn main() {
    let s = String::from("hello");
    takes_ownership(s);      // s的所有权移入函数
    // println!("{}", s);    // 报错！s在函数调用后就失效了

    let x = 5;
    makes_copy(x);           // x是Copy类型，复制进函数
    println!("{}", x);       // 没问题，x还在
}

fn takes_ownership(some_string: String) {
    println!("{}", some_string);
}  // some_string在这里被drop，内存释放

fn makes_copy(some_integer: i32) {
    println!("{}", some_integer);
}
```

---

## Java工程师的困惑解答

**Q：这不是很麻烦吗？传个参数还要考虑所有权？**

A：Rust提供了"借用"（&引用）来解决这个问题，不需要转移所有权。
这是明天Day3的主题。今天先接受"Move语义存在"这个事实。

**Q：String和&str有什么区别？**

| | String | &str |
|--|--------|------|
| 类比Java | `StringBuilder` / 堆上String | 字符串切片，只读引用 |
| 所有权 | 有所有权 | 借用，不拥有 |
| 可变 | 可以 `mut` | 不可以 |
| 常用场景 | 需要修改/拥有字符串 | 只读查看 |

---

## 今日完成检查

- [ ] 4个实验代码都手敲并运行了
- [ ] 能解释为什么实验1中 `println!("{}", s1)` 会报错
- [ ] 理解Copy类型和Move类型的区别
- [ ] 能用自己的话解释"所有权转移"是什么意思

---

## 自我测试

下面这段代码有几个问题？能找出来吗？

```rust
fn main() {
    let s1 = String::from("world");
    let s2 = s1;
    let s3 = s1.clone();
    println!("{} {} {}", s1, s2, s3);
}
```

答案：（自己先思考，再发给Claude验证）
