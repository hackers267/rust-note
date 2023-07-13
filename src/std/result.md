# Result

`Result`是Rust中处理错误最最常用的一个类型。其和[Option](./option.md)一样，也是一个枚举体，只是[Option](./option.md)是由`Some(T)`和`None`结成，
用于表示数据的有和无。而`Result`则是由`Ok(T)`和`Err(E)`组成，用于表示结果的成功和错误。

`Result`的枚举体如下：
```rust
enum Result<T, E> {
  Ok(T),
  Err(E),
}
```

## Result和`?`操作符

`?`操作符是`Result`使用最多的操作符之一，其使用方式如下：
```rust
use std::fs::File;
use std::io::prelude::*;
use std::io;

struct Info {
    name: String,
    age: i32,
    rating: i32,
}

fn write_info(info: &Info) -> io::Result<()> {
    let mut file = File::create("my_best_friends.txt")?;
    file.write_all(format!("name: {}\n", info.name).as_bytes())?;
    file.write_all(format!("age: {}\n", info.age).as_bytes())?;
    file.write_all(format!("rating: {}\n", info.rating).as_bytes())?;
    Ok(())
}
```
