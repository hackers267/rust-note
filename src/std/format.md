# format!

`format!`宏是rust中格式化字符串最常使用的一个方法。通过`format!`宏不仅可以拼接字符串，而且可以转换字符串，格式化字符串。

## 拼接字符串

`format!`宏可以直接拼写字符串。下面就是其中的示例代码：

```rust
let first = "hello";
let second = "world";
let s = format!("{first} {second}!");
println!("{s}");
```
也可以使用下面这样的代码：
```rust
let first = "hello";
let second = "world";
let s = format!("{} {}!",first,second);
println!("{}",s);
```

## 格式化字符串

```rust
let s = format!("{:04}",12);
println!("{s}");
```