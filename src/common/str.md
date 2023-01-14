# 字符串

## 获取字符

因为在`Rust`中字符串中的特殊实现，字符串是不支持索引的。如果想要获取字符串中的某个字符，我们可以使用`chars`来生成一个字符迭代器。比如：

```rust
fn main(){
    let str = "我是中国人";
    str.chars()
        .for_each(|char| {
            println!("{char}");
        })
}
```

## 对字符串的操作

### 追加

对字符串的追加操作由于要**修改原字符串**，所以这个操作只针对`String`类型，而且需要添加`mut`修饰。
示例：
```rust
fn main(){
    let mut str = String::from("我是");
    str.push_str("中国人");
    println!("{str}");
    str.push('!');
    println!("{str}");
}
```

### 插入

对字符串的插入操作和追加操作一样，会**修改原字符串**。其和追加的区别在于，插入可以把字符或字符串插入到原字符串的任意位置。
示例：
```rust
fn main(){
    let mut str = String::from("中国人");
    str.insert_str(0,"我是");
    println!("{str}");
    str.insert(0,'@');
    println!("{str}");
}
```

> 需要注意的是，当使用插入方法的时候，因为插入位置是一个索引，所以需要确保索引合法。

### 替换

Rust中，与替换字符串有关的方法有三个：`repalce`,`repalcen`,`repalce_range`

1.`replace`

`replace`方法不会修改原字符串，而是生成一个新的字符串，所以对`&str`和`String`都可以使用。
示例：
```rust
fn main() {
    let like = "I like rust";
    let love = like.replace("like", "Love");
    println!("{love}");
}
```

2.`replacen`

`replacen`方法和`replace`方法相似，只是`repalcen`方法比`repalce`多一个参数，用于指定需要替换多少个字符串。
示例：
```rust
fn main() {
    let like = "I like rust and I like cargo";
    let love = like.replace("like","love");
    println!("{love}");
    let love = like.replacen("like","love",1);
    println!("{love}");
}
```

3.`replace_range`

`replace_range`方法和上面的两个方法不同，`repalce_range`方法会**修改原字符串**，所以，只适用于`String`类型，且必须使用`mut`修饰。
示例：
```rust
fn main(){
    let mut like = String::from("I like rust");
    like.replace_range(7..11,"Cargo");
    println!("{like}");
}
```

### 删除

和字符串删除有关的方法有4个：
- pop
- remove
- truncate
- clear

这4个方法都会**修改原来的字符串**，只适用于**String**类型。

1.`pop`

`pop`方法会取出字符串中的最后一个字符。
示例：
```rust
fn main(){
    let mut str = String::from("我是中国人");
    while let Some(char) = str.pop() {
        println!("{char}");
    }
}
```

2.`remove`

`remove`方法删除指定索引处的字符。如果指定的索引处不合法，那么这个方法会直接报错。如果索引合法，就返回删除的字符。

示例：
```rust
fn main(){
    let mut str = String::from("I like Rust!");
    let char = str.remove(5);
    println!("{str}:{char}");
}
```

3.`truncate`

`truncate`方法会删除指定索引处到结尾的所有字符，**没有返回值**。如果指定的索引不合法，则会发生错误。

示例：
```rust
fn main(){
    let mut str = String::from("I like rust!");
    str.truncate(6);
    println!("{str}");
}
```

4.`clear`

`clear`方法会把字符串的所有字符清空。
示例：
```rust
fn main() {
    let mut str = String::from("I like Rust");
    str.clear();
    println!("{str}");
}
```

### 连接字符串

在rust中，当我们需要连接两个或多个字符串的时候，我们可以使用`+`运算符或使用`format!`宏。

1.使用`+`或`+=`运算符

示例：
```rust ,editable
fn main() {
    let who = String::from("I");
    let love = "love";
    let lang = "rust";
    let str = who + love + lang;
    println!("{str}");
}
```

2.使用`format!`宏

示例：
```rust ,editable
fn main() {
    let who = String::from("I");
    let love = "love";
    let lang = "rust";
    let str = format!("{} {} {}!",who,love,lang);
    println!("{str}");
}
```
