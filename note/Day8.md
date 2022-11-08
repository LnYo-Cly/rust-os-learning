今日学习：
+ enums 枚举
+ module 模块
+ string
# 枚举

```RUST
// 声明方式
enum PokerSuit {
  Clubs,
  Spades,
  Diamonds,
  Hearts
}


//----


enum PokerCard {
    Clubs(u8),
    Spades(u8),
    Diamonds(char),
    Hearts(char),
}

fn main() {
   let c1 = PokerCard::Spades(5);
   let c2 = PokerCard::Diamonds('A');
}

//---
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor1(i32, i32, i32),
    ChangeColor2((i32, i32, i32)),
}//可以当作，（）里面写参数

fn main() {
    let m1 = Message::Quit;
    let m2 = Message::Move{x:1,y:1};
    let m3 = Message::ChangeColor1(255,255,0);
    let m4 = Message::ChangeColor2((255,255,0));
}


```

# 模块

+ 文件就是一个模块
+ mod {  }

多个文件定义为一个模块

btree/  mod.rs

```RUST

mod 模块名{

    fn 函数{

    }

    //可见性是以模块为界
    pub fn 函数{


    }

}
// 模块中的东西（函数，常量等等）要想使用，需要加上对应权限

```