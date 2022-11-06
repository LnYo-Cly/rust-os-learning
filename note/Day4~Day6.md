Day4和Day5的课和作业实在太多了，所以这两天几乎没有在学rust。

今日学习：

+ primitive_types
+ vecs
+ mov语义


 国外的一个视频：[Visualizing memory layout of Rust's data types](https://www.youtube.com/watch?v=rDoqT-a6UFg) 



# 切片
> 它允许你引用集合中部分连续的元素序列，而不是引用整个集合。



```RUST
// [开始索引..终止索引] 左闭右开[....)
let a = [1, 2, 3, 4, 5];
let slice = &a[1..3];


```
> 切片是不定长类型，rust在编译器会进行检查，不能绑定不定长的，必须要定长

# 数组类型

```RUST
//基本创建方法
let a = [1, 2, 3, 4, 5];
let a: [i32; 5] = [1, 2, 3, 4, 5];

//初始化固定长度 [元素; 长度]
let a = [3; 5]; 
```

# tuple

```RUST
//基本创建方法
let a = ("123",123);

let (s,n) = a;

// 获取元素方法
a.0
a.1

```

# Vector

```RUST
let mut v = Vec::new();
v.push(1);


let v = vec![1, 2, 3];

```


```RUST
// 读取vec
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("第三个元素是 {}", third);

match v.get(2) {
    Some(third) => println!("第三个元素是 {}", third),
    None => println!("去你的第三个元素，根本没有！"),
}

//-----
let v = vec![1, 2, 3, 4, 5];

let does_not_exist = &v[100];
let does_not_exist = v.get(100);

// get 不会引起越界错误

```


```RUST
// 遍历Vec
let v = vec![1, 2, 3];
for i in &v {
    println!("{}", i);
}

// --

let mut v = vec![1, 2, 3];
for i in &mut v {
    *i += 10
}

```