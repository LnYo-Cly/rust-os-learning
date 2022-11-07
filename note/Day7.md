今日学习：
+ 继续完成mov语义
+ 结构体


# mov语义
+ 当发生从新绑定时，旧的绑定无法再使用
+ 当函数传参时，也会发生mov，可以考虑利用clone解决
+ 不可变引用可以同时又多个，可变引用只能有一个

# 结构体
结构体有很多

例如：
+ 跟c语言很像的classic c struct
+ 元组结构体TupleStruct
+ 类单元结构体unit-like struct（里面没有任何字段）

```RUST
struct ColorClassicStruct {
    // TODO: Something goes here
    red: u8,
    green: u8,
    blue: u8
}

struct ColorTupleStruct(u8, u8, u8);

#[derive(Debug)]
struct UnitLikeStruct;
```

```RUST
//利用模板更新
//当两个结构体很像，我们可以直接先定义不相同的部分，剩下的部分利用模板自动填充

let order_template = Order {
        name: String::from("Bob"),
        year: 2019,
        made_by_phone: false,
        made_by_mobile: false,
        made_by_email: true,
        item_number: 123,
        count: 0,
}


let your_order = Order{
    name: String::from("Hacker in Rust"),
    count: 1,
    ..order_template
};

// 这样，your_order中的name和count不同，其他字段都是相同的值

```