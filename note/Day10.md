今日学习：

+ option
+ if let
+ while let
+ err_handling

# option

Option是一个枚举类型，要么有值，要么为None

```RUST

enum Option<T> {
    Some(T),
    None,
}


```

# if let/while let

if let 和 while let 都是跟match一样属于模式匹配。

可以认为 some(a) = b, 当b为some()时，可以匹配到

详细可以看options2的例子，

# err_handling

```RUST

// 可恢复错误
enum Result<T,E> {
    Ok<T>,
    Err<E>
}

// ？表达式：错误传播
在一些可能会操作失败的函数（如，读取文件），后面加上?，支持链式

File::open("hello.txt")?.read_to_string(&mut s)?;


dyn是trait对象类型的前缀 dyn关键字用于强调相关trait的方法是动态分配的。

```