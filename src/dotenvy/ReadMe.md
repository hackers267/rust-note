# dotenvy

`dotenvy`是一个`dotenv`的克隆版本。其开发目的是`dotenv`的上次发布已经是多年前的事情了。开发`dotenvy`是为了在`dotenv`的基础之上修复已有问题和添加新的特性。仓库地址:[dotenvy](https://crates.io/crates/dotenvy)

## 基本使用

```rust
use dotenvy::dotenv;
use std::env;

fn main() {
    dotenv().ok();

    for (key,value) in env::vars() {
        println!("{key}: {value}");
    }
}
```

