今日学习：

+ thread 线程
+ macro 宏
+ clippy 代码检查 lint 工具
+ conversion 类型转换

# thread 线程

```RUST
use std::thread;

// MOVE 会拿走所有权
thread::spawn(move || {
    // some work here
});


// 等待子线程执行完毕
let res = child.join();


// 配置线程（名称，堆栈大小）
thread::Builder::new().name("child1".to_string()).spawn(move || {
    println!("Hello, world!");
});

```

# macro 宏
[有关marco的文章](http://blog.hubwiz.com/2020/01/30/rust-macro/)

[圣经关于marco部分](https://course.rs/advance/macro.html#macro-%E5%AE%8F%E7%BC%96%E7%A8%8B)

```RUST
// 定义多个宏
// 第一个文章的定义宏

macro_rules! my_macro {
    () => {
        println!("Check out my macro!");
    };
    ($val:expr) => {
        println!("Look at this other macro: {}", $val);
    }
}

fn main() {
    my_macro!();
    my_macro!(7777);
}


```

# clippy 代码检查 lint 工具

Clippy 是 Rust 官方提供的 代码检查 lint 工具，通过静态分析，来检查代码中有问题或不符合指定规范的代码。

