[Open-Source-OS-Training-Camp-2022 文档](https://learningos.github.io/rust-based-os-comp2022/chapter1/0intro.html#id4)

访问 [训练营 kernel labs](https://github.com/LearningOS/rust-based-os-comp2022#kernel-labs)，点击下列链接，形成各个自己实验专用 repos：

- [lab0-0 实践](https://classroom.github.com/a/hnoWuKGF)
- [lab0-1 实践](https://classroom.github.com/a/UEOvz4qO)

- [lab1 实验](https://classroom.github.com/a/s1v7GyJM)
- [lab2 实验](https://classroom.github.com/a/ghbB1wYX)
- [lab3 实验](https://classroom.github.com/a/RxB6h4-x)
- [lab4 实验](https://classroom.github.com/a/94eMW8zi)
- [lab5 实验](https://classroom.github.com/a/zqGJEPK-)

请注意各个实践或实验的具体初始化设置：

- [lab0-0 实践初始化设置](https://learningos.github.io/rust-based-os-comp2022/chapter1/0intro.html#id4)
- [lab0-1 实践初始化设置](https://learningos.github.io/rust-based-os-comp2022/chapter2/0intro.html#id3)
- [lab1 实验初始化设置](https://learningos.github.io/rust-based-os-comp2022/chapter3/0intro.html#id3)
- [lab2 实验初始化设置](https://learningos.github.io/rust-based-os-comp2022/chapter4/0intro.html#id3)
- [lab3 实验初始化设置](https://learningos.github.io/rust-based-os-comp2022/chapter5/0intro.html#id3)
- [lab4 实验初始化设置](https://learningos.github.io/rust-based-os-comp2022/chapter6/0intro.html#id3)
- [lab5 实验初始化设置](https://learningos.github.io/rust-based-os-comp2022/chapter8/0intro.html#id5)


# 环境配置

由于第一次，需要先进行一下环境的配置。

```BASH
# 我这里是本地的Ubuntu环境，运行下面命令，一次就行
sudo make ubuntu_local_setenv

# 让GitHub classroom自动评测
make setupclassroom_test1


```
+ lab0-0 : test1
+ lab0-1：test2
+ lab1：test3
+ lab2：test4
+ lab3：test5
+ lab4：test6
+ lab5：test8


```BASH
#由于自动安装命令有问题一直不成功，改成手动安装
# 安装编译所需的依赖包
sudo apt install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev \
              gawk build-essential bison flex texinfo gperf libtool patchutils bc \
              zlib1g-dev libexpat-dev pkg-config  libglib2.0-dev libpixman-1-dev git tmux python3 ninja-build
# 下载源码包
# 如果下载速度过慢可以使用我们提供的百度网盘链接：https://pan.baidu.com/s/1z-iWIPjxjxbdFS2Qf-NKxQ
# 提取码 8woe
wget https://download.qemu.org/qemu-7.0.0.tar.xz
# 解压
tar xvJf qemu-7.0.0.tar.xz
# 编译安装并配置 RISC-V 支持
cd qemu-7.0.0
./configure --target-list=riscv64-softmmu,riscv64-linux-user
make -j$(nproc)

sudo make install


qemu-system-riscv64 --version
qemu-riscv64 --version




```

# 运行

根据实验指导书上的内容，运行如下

```BASH
cd os1
LOG=DEBUG make run

```

![20221127190740](https://cdn.jsdelivr.net/gh/lnyo-cly/blogImg/pics/20221127190740.png)



# 

![20221127212427](https://cdn.jsdelivr.net/gh/lnyo-cly/blogImg/pics/20221127212427.png)

编译器在编译、链接得到可执行文件时需要知道，程序要在哪个 平台 (Platform) 上运行， 目标三元组 (Target Triplet) 描述了目标平台的 CPU 指令集、操作系统类型和标准运行时库。

```BASH
rustc --version --verbose #该命令可以查看对应的目标三元组


cargo run --target riscv64gc-unknown-none-elf # 可以修改目标平台


```

为了每次都编译为目标平台，做如下操作：

```BASH
rustup target add riscv64gc-unknown-none-elf # 添加目标平台
```

在 os 目录下新建 .cargo 目录，并在这个目录下创建 config 文件，输入如下内容：
```RUST
# os/.cargo/config
[build]
target = "riscv64gc-unknown-none-elf"
```


交叉编译 (Cross Compile)：这种编译器运行的平台（x86_64）与可执行文件运行的目标平台（编译器编译出的文件）不同的情况。



加上 *#![no_std]* 移除 !println宏，移除了std还要加上 *#![no_main]*移除main

由于 *#[panic_handler]* 错误处理函数在std中，所以要用core自己实现一个


```BASH
cargo install cargo-binutils # 安装分析工具
rustup component add llvm-tools-preview




[文件格式]
$ file target/riscv64gc-unknown-none-elf/debug/os
target/riscv64gc-unknown-none-elf/debug/os: ELF 64-bit LSB executable, UCB RISC-V, ......

[文件头信息]
$ rust-readobj -h target/riscv64gc-unknown-none-elf/debug/os
   File: target/riscv64gc-unknown-none-elf/debug/os
   Format: elf64-littleriscv
   Arch: riscv64
   AddressSize: 64bit
   ......
   Type: Executable (0x2)
   Machine: EM_RISCV (0xF3)
   Version: 1
   Entry: 0x0
   ......
   }

[反汇编导出汇编程序]
$ rust-objdump -S target/riscv64gc-unknown-none-elf/debug/os
   target/riscv64gc-unknown-none-elf/debug/os:       file format elf64-littleriscv
```


下面开始对环境初始化：

先手动提供一个入口函数_start()
```RUST
#[no_mangle]
extern "C" fn _start() {
    loop{};
}
```

加上退出机制代码

```RUST
// os/src/main.rs

// 本段代码目前不需要深入
const SYSCALL_EXIT: usize = 93;

fn syscall(id: usize, args: [usize; 3]) -> isize {
    let mut ret;
    unsafe {
        core::arch::asm!(
            "ecall",
            inlateout("x10") args[0] => ret,
            in("x11") args[1],
            in("x12") args[2],
            in("x17") id,
        );
    }
    ret
}

pub fn sys_exit(xstate: i32) -> isize {
    syscall(SYSCALL_EXIT, [xstate as usize, 0, 0])
}

#[no_mangle]
extern "C" fn _start() {
    sys_exit(9);
}

```


```BASH

[运行程序]
[打印程序的返回值]
$ qemu-riscv64 target/riscv64gc-unknown-none-elf/debug/os; echo $?

```


到这里已经实现了可以运行的程序。

下面开始实现字符串的输出：

首先封装一下对 SYSCALL_WRITE 系统调用。
```RUST
// os/src/main.rs

const SYSCALL_WRITE: usize = 64;

pub fn sys_write(fd: usize, buffer: &[u8]) -> isize {
  syscall(SYSCALL_WRITE, [fd, buffer.as_ptr() as usize, buffer.len()])
}
```

然后实现基于 Write Trait 的数据结构，并完成 Write Trait 所需要的 write_str 函数，并用 print 函数进行包装。最后，基于 print 函数，实现Rust语言 格式化宏 ( formatting macros )。

```RUST
//  os/src/console.rs
use core::fmt::{Write, Arguments, Result};
use crate::sys_write;

struct Stdout;

impl Write for Stdout {
    fn write_str(&mut self, s: &str) -> Result {
        sys_write(1, s.as_bytes());
        Ok(())
    }
}

pub fn print(args: Arguments) {
    Stdout.write_fmt(args).unwrap();
}

macro_rules! print {
    ($fmt: literal $(, $($arg: tt)+)?) => {
        $crate::console::print(format_args!($fmt $(, $($arg)+)?));
    }
}

macro_rules! println {
    ($fmt: literal $(, $($arg: tt)+)?) => {
        $crate::console::print(format_args!(concat!($fmt, "\n") $(, $($arg)+)?));
    }
}

```

重新build并且运行，可以成功打印出字符串了

![20221127231413](https://cdn.jsdelivr.net/gh/lnyo-cly/blogImg/pics/20221127231413.png)



上面实现的是用户态，我们需要将用户态迁移到内核态：

用 QEMU 软件 qemu-system-riscv64 来模拟 RISC-V 64 计算机。加载内核程序的命令如下：
```BASH
qemu-system-riscv64 \
            -machine virt \
            -nographic \
            -bios $(BOOTLOADER) \
            -device loader,file=$(KERNEL_BIN),addr=$(KERNEL_ENTRY_PA)
```

+ -bios $(BOOTLOADER) 意味着硬件加载了一个 BootLoader 程序，即 RustSBI

+ -device loader,file=$(KERNEL_BIN),addr=$(KERNEL_ENTRY_PA) 表示硬件内存中的特定位置 $(KERNEL_ENTRY_PA) 放置了操作系统的二进制代码 $(KERNEL_BIN) 。 $(KERNEL_ENTRY_PA) 的值是 0x80200000 。

当我们执行包含上述启动参数的 qemu-system-riscv64 软件，就意味给这台虚拟的 RISC-V64 计算机加电了。 此时，CPU 的其它通用寄存器清零，而 PC 会指向 0x1000 的位置，这里有固化在硬件中的一小段引导代码， 它会很快跳转到 0x80000000 的 RustSBI 处。 RustSBI完成硬件初始化后，会跳转到 $(KERNEL_BIN) 所在内存位置 0x80200000 处， 执行操作系统的第一条指令

![20221127231802](https://cdn.jsdelivr.net/gh/lnyo-cly/blogImg/pics/20221127231802.png)

> RustSBI: SBI 是 RISC-V 的一种底层规范，RustSBI 是它的一种实现。 操作系统内核与 RustSBI 的关系有点像应用与操作系统内核的关系，后者向前者提供一定的服务。只是SBI提供的服务很少， 比如关机，显示字符串等。


```RUST
QEMU有两种运行模式：

User mode 模式，即用户态模拟，如 qemu-riscv64 程序， 能够模拟不同处理器的用户态指令的执行，并可以直接解析ELF可执行文件， 加载运行那些为不同处理器编译的用户级Linux应用程序。

System mode 模式，即系统态模式，如 qemu-system-riscv64 程序， 能够模拟一个完整的基于不同CPU的硬件系统，包括处理器、内存及其他外部设备，支持运行完整的操作系统。

```