今天开始第二章的学习

这一章的目的是实现批处理系统，可以一次批处理运行多个程序。

保护 操作系统不受出错程序破坏的机制被称为 特权级 (Privilege) 机制， 它实现了用户态和内核态的隔离。

[详细的特权级机制](http://rcore-os.cn/rCore-Tutorial-Book-v3/chapter2/1rv-privilege.html)




```RUST
 1// user/src/syscall.rs
 2
 3fn syscall(id: usize, args: [usize; 3]) -> isize {
 4   let mut ret: isize;
 5   unsafe {
 6       core::arch::asm!(
 7           "ecall",
 8           inlateout("x10") args[0] => ret,
 9           in("x11") args[1],
10           in("x12") args[2],
11           in("x17") id
12       );
13   }
14   ret
15}

```
这段代码将所有的系统调用都封装成 syscall 函数。

Rust 编译器无法判定汇编代码的安全性，所以我们需要将其包裹在 unsafe 块中。

第一行为汇编模板，后面的参数就是模板中的参数。

模板可以有多个

[内联汇编-asm宏](https://doc.rust-lang.org/nightly/reference/inline-assembly.html)

[汉化版](https://runebook.dev/zh-CN/docs/rust/reference/inline-assembly)

[详细的内联汇编](https://xiaopengli89.github.io/posts/rust-asm-macro/)




由于 global_asm! 存在于函数之外，它不能使用输入/输出操作数。


![20221128220334](https://cdn.jsdelivr.net/gh/lnyo-cly/blogImg/pics/20221128220334.png)


当 CPU 执行完一条指令并准备从用户特权级 陷入（ Trap ）到 S 特权级的时候，硬件会自动完成如下这些事情：

sstatus 的 SPP 字段会被修改为 CPU 当前的特权级（U/S）。

+ sepc 会被修改为 Trap 处理完成后默认会执行的下一条指令的地址。

+ scause/stval 分别会被修改成这次 Trap 的原因以及相关的附加信息。

+ CPU 会跳转到 stvec 所设置的 Trap 处理入口地址，并将当前特权级设置为 S ，然后从Trap 处理入口地址处开始执行。