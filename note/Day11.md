今日学习：

+ generics 泛型
+ Trait 特征
+ quiz3
+ test
+ lifetime 生命周期
+ standard_library_type

# generics 泛型

泛型就是一种多态

相当于C++的模板

```RUST

// 基本形式
fn add<T>(a:T, b:T) -> T {
    a + b
}

fn main() {
    println!("add i8: {}", add(2i8, 3i8));
    println!("add i32: {}", add(20, 30));
    println!("add f64: {}", add(1.23, 1.23));
}

```


```RUST

// 结构体使用泛型
struct Point<T> {
    x: T,
    y: T,
}

struct Point<T,U> {
    x: T,
    y: U,
}

// 枚举使用泛型
enum Option<T> {
    Some(T),
    None,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}



```


```RUST

// 方法中使用泛型，要声明impl<T>
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

// 如果实现具体类型，只有这个类型才有对应方法，其他的类型没有
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

```


```RUST
// const泛型（值泛型）
// const N: usize 这里就是定义const泛型
fn display_array<T: std::fmt::Debug, const N: usize>(arr: [T; N]) {
    println!("{:?}", arr);
}
fn main() {
    let arr: [i32; 3] = [1, 2, 3];
    display_array(arr);

    let arr: [i32; 2] = [1, 2];
    display_array(arr);
}


```

# Trait 特征

Trait特征 可以理解为 java 中的 接口

```RUST
// 声明方式
pub trait Summary {
    fn summarize(&self) -> String;
}

```

```RUST
// 为一个类型实现一个具体的特征
// impl 特征 for 类型 {}


pub trait Summary {
    fn summarize(&self) -> String;
}
pub struct Post {
    pub title: String, // 标题
    pub author: String, // 作者
    pub content: String, // 内容
}

impl Summary for Post {
    fn summarize(&self) -> String {
        format!("文章{}, 作者是{}", self.title, self.author)
    }
}

pub struct Weibo {
    pub username: String,
    pub content: String
}

impl Summary for Weibo {
    fn summarize(&self) -> String {
        format!("{}发表了微博{}", self.username, self.content)
    }
}

// 孤儿规则： 要想实现一个特征，特征和类型的定义，最少有一个要在当前作用域定义

```

```RUST
// 默认实现
// 可以直接使用，当然也可以根据需求再次重写
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}



```

```RUST
// 特性作为函数中的参数
// 用于需要一个函数作为参数，如回调函数等等
fn output(object: impl Descriptive) {
    println!("{}", object.describe());
}

pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}


// 特征约束
// impl Trait是一种语法题，跟这一样，只是上面看着更好理解
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}

// 多重约束
pub fn notify(item: &(impl Summary + Display)) {}
pub fn notify<T: Summary + Display>(item: &T) {}

// 当约束变多时，可以使用where约束
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {}


```

```RUST
// 特征做为返回值

fn person() -> impl Descriptive {
    Person {
        name: String::from("Cali"),
        age: 24
    }
}
//特性做返回值只接受实现了该特性的对象做返回值且在同一个函数中所有可能的返回值类型必须完全一样

```

# 生命周期

```RUST
// 一般情况下，编译器会自动推导生命周期
//  当多个生命周期存在，且编译器无法推导出某个引用的生命周期时，就需要我们手动标明生命周期

&i32        // 常规引用
&'a i32     // 含有生命周期注释的引用
&'a mut i32 // 可变型含有生命周期注释的引用

struct Str<'a> {
        content: &'a str
}
impl<'a> Str<'a> {
    fn get_content(&self) -> &str {
        self.content
    }
}

// 静态生命周期
生命周期注释有一个特别的：'static 。所有用双引号包括的字符串常量所代表的精确数据类型都是 &'static str ，'static 所表示的生命周期从程序运行开始到程序运行结束。
```


