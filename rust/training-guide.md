# Java转Rust — 训练说明

## 每次练习告诉Claude

```
"今天学Rust Week X Day X，主题是[XXX]。
我有[Java]背景，用Java类比帮我解释。
我遇到的问题是：[...]"
```

## 最重要的三件事

1. **每天手敲代码**，不要复制——手敲才会触发记忆
2. **让编译器报错**——Rust编译器错误信息是最好的老师
3. **每个概念对应Java**——你有10年Java经验，这是最大优势

## Rustlings 安装

```bash
cargo install rustlings
rustlings init
rustlings watch   # 启动练习模式
```

## 推荐学习顺序

1. The Rust Book（https://doc.rust-lang.org/book/）
2. 本工程的每日练习文件
3. Rustlings 配套练习
4. 每周一个小项目（见学习计划总览）

## 编译器错误不是敌人

Java工程师学Rust最大的心理障碍：**编译器总是拒绝你**

正确心态：Rust编译器是你的同事，它帮你在编译期发现了
生产环境的并发bug、内存泄漏、null指针。
报错 = 它帮你省了几个小时的生产排查。
