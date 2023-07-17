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

## 方法概览

### 查询变体

- is_ok 判断`Result`是否是`Ok`
- is_err 判断`Result`是否是`Err`
- is_ok_and `Result`是`Ok`变体并符合给定的断言
- is_err_and `Result`是`Err`变体饮符合给定的断言

#### is_ok

`is_ok`是用来查看一个`Result`是否是`Ok`的变体，如果`Result`是`Ok`变体，这个函数会返回`true`,否则返回`false`;

签名如下：

```rust
impl<T, E> Result<T, E> {
  pub const fn is_ok(&self) -> bool
}
```

示例代码：

```rust
let x: Result<i32, &str> = Ok(-32);
assert_eq!(x.is_ok(), true);

let x: Result<i32, &str> = Err("Some error message");
assert_eq!(x.is_ok(), false);
```

#### is_err

`is_err`用来检查一个`Result`是不是`Err`变体，如果`Result`是`Err`变体，函数返回`true`,否则返回`false`;

签名如下：

```rust
impl<T, E> Result<T, E> {
  pub const fn is_err(&self) -> bool
}
```

代码示例：

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.is_err(), false);

let x: Result<u32, &str> = Err("Some error message");
assert_eq!(x.is_err(), true);
```

#### is_ok_and

`is_ok_and`用来检查一个`Result`是否是`Ok`变体并符合给定的断言。如果`Result`是`Ok`变体并符合给定的断言，函数返回`true`，否则返回`false`.

签名如下：

```rust
impl<T, E> Result<T, E> {
  pub const fn is_ok_and<F>(&self, f: F) -> bool
   where F: impl FnOnce(T) -> bool
}
```

示例代码：

```rust
let x:Result<i32, &str> = Ok(2);
assert_eq!(x.is_ok_and(|x| x > 0), true);

let x:Result<u32, &str> = Ok(0);
assert_eq!(x.is_ok_and(|x| x > 0), false);

let x:Result<u32, &str> = Err("hey");
assert_eq!(x.is_ok_and(|x| x > 0), false);
```

#### is_err_and

`is_err_and`用来检查一个`Result`是否是`Err`变体并符合给定的断言。

- 是`Ok`变体，符合给定的断言 -> true
- 是`Ok`变体，不符合给定的断言 -> false
- 是`Err`变体 -> false

签名如下：

```rust
impl<T, E> Result<T, E> {
  pub const fn is_err_and<F>(&self, f: F) -> bool
   where F: impl FnOnce(E) -> bool
}
```

示例代码：

```rust
use std::io::{Error, ErrorKind};

let x: Result<u32, Error> = Err(Error::new(ErrorKind::NotFound, "!"));
assert_eq!(x.is_err_and(|x| x.kind() == ErrorKind::NotFound), true);

let x: Result<u32, Error> = Err(Error::new(ErrorKind::PermissionDenied, "!"));
assert_eq!(x.is_err_and(|x| x.kind() == ErrorKind::NotFound), false);

let x: Result<u32, Error> = Ok(123);
assert_eq!(x.is_err_and(|x| x.kind() == ErrorKind::NotFound), false);
```

### 引用相关

- as_ref    转换`&Result<T, E>` 为 `Result<&T, &E>`
- as_mut    转换`&mut Result<T, E>` 为 `Result<&mut T, &mut E>`
- as_deref  转换`&Result<T, E>` 为 `Result<&T::Target, &E>`
- as_deref_mut  转换`&mut Result<T, E>` 为 `Result<&mut T::Target, &mut E>`

#### as_ref

`as_ref`用来转换`&Result<T, E>` 为 `Result<&T, &E>`.

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn as_ref(&self) -> Result<&T, &E>
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.as_ref(), Ok(&2));

let x: Result<u32, &str> = Err("Some error message");
assert_eq!(x.as_ref(), Err(&"Some error message"));
```

#### as_mut

`as_mut`用来转换`&mut Result<T, E>` 为 `Result<&mut T, &mut E>`.

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn as_mut(&mut self) -> Result<&mut T, &mut E>
}
```

代码示例:

```rust
fn mutate(r: &mut Result<i32, i32>) {
    match r.as_mut() {
        Ok(v) => *v = 42,
        Err(e) => *e = 0,
    }
}

let mut x: Result<i32, i32> = Ok(2);
mutate(&mut x);
assert_eq!(x.unwrap(), 42);

let mut x: Result<i32, i32> = Err(13);
mutate(&mut x);
assert_eq!(x.unwrap(), 0);
```

#### as_deref

`as_deref`用来转换`&Result<T, E>` 为 `Result<&<T as Deref>::Target, &E>`.

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn as_deref(&self) -> Result<&<T as Deref>::Target, &E>
  where
    T: Deref
}
```

代码示例:

```rust
let x: Result<String, u32> = Ok("hello".to_string());
let y: Result<&str, &u32> = Ok("hello");
assert_eq!(x.as_deref(), y);f

let x: Result<String, u32> = Err(13);
let y: Result<&str, &u32> = Err(&13);
assert_eq!(x.as_deref(), y);
```

#### as_deref_mut

`as_deref_mut`用来转换`&mut Result<T, E>` 为 `Result<&mut <T as DerefMut>::Target, &mut E>`.

签名如下：

```rust
impl<T, E> Result<T, E> {
  pub fn as_deref_mut(&mut self) -> Result<&mut <T as DerefMut>::Target, &mut E>
  where
    T: DerefMut
}
```

代码示例:

```rust
let mut s = "HELLO".to_string();
let mut x: Result<String, u32> = Ok("hello".to_string());
let y: Result<&mut str, &mut u32> = Ok(&mut s);
assert_eq!(x.as_deref_mut().map(|x| { x.make_ascii_uppercase(); x }), y);

let mut i = 42;
let mut x: Result<String, u32> = Err(42);
let y: Result<&mut str, &mut u32> = Err(&mut i);
assert_eq!(x.as_deref_mut().map(|x| { x.make_ascii_uppercase(); x }), y);
```

### 提取内部值

- [expect](#expect)
- [unwrap](#unwrap)
- [unwrap_or](#unwrap_or)
- [unwrap_or_default](#unwrap_or_default)
- [unwrap_or_else](#unwrap_or_else)
- [expect_err](#expect_err)
- [unwrap_err](#unwrap_err)
- [unwrap_unchecked](#unwrap_unchecked)
- [unwrap_err_unchecked](#unwrap_err_unchecked)

#### expect

`expect`用来提取`Result<T, E>`的内部值。

- `Ok`变体 -> 返回`Ok`的内部值
- `Err`变体 -> 发生`panic`并输出自定义的错误信息

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn expect(self, msg: &str) -> T
  where
   E: Debug
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.expect("Error"), 2);

let x: Result<u32, &str> = Err("Error");
x.expect("Error"); // panic
```

#### unwrap

`unwrap`用来提取`Result<T, E>`的内部值。

- `Ok`变体 -> 返回`Ok`的内部值
- `Err`变体 -> panic,输出默认的错误信息

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn unwrap(self) -> T
  where 
    E: Debug
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.unwrap(), 2);

let x: Result<u32, &str> = Err("failure");
x.unwrap(); // panic
```

#### unwrap_or

`unwrap_or`用来提取`Result<T, E>`的内部值。

- `Ok`变体 -> 返回`Ok`的内部值
- `Err`变体 -> 返回`unwrap_or`提供的默认值

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn unwrap_or(self, default: T) -> T
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.unwrap_or(0), 2);

let x: Result<u32, &str> = Err("failure");
assert_eq!(x.unwrap_or(0), 0);
```

#### unwrap_or_default

`unwrap_or_default`用来提取`Result<T, E>`的内部值。

- `Ok`变体 -> 返回`Ok`的内部值
- `Err`变体 -> 返回类型`T`的默认值

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn unwrap_or_default(self) -> T
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.unwrap_or_default(), 2);

let x: Result<u32, &str> = Err("failure");
assert_eq!(x.unwrap_or_default(), 0);
```

#### unwrap_or_else

`unwrap_or_else`用来提取`Result<T, E>`的内部值。

- `Ok`变体 -> 返回`Ok`的内部值
- `Err`变体 -> 返回`unwrap_or_else`提供的函数的返回结果

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn unwrap_or_else(self, f: impl FnOnce() -> T) -> T
}
```

代码示例：

```rust
fn count(x: &str) -> uszie { x.len() }

assert_eq!(Ok(2).unwrap_or_else(count), 2);
assert_eq!(Err("foo").unwrap_or_else(count), 3);
```

#### expect_err

`expect_err`用来提取`Result<T, E>`的内部值。

- `Ok`变体 -> *panic*并输出自定义错误信息
- `Err`变体 -> 返回`Err`中的内部值

签名如下：

```rust
impl<T, E> Result<T, E> {
    pub fn expect_err(self, msg: &str) -> E
    where T: Debug
}
```

代码示例：

```rust
let x: Result<u32, &str> = Ok(10);
x.expect_err("Testing expect_err"); // panic
```

#### unwrap_err

`unwrap_err`用来提取`Result<T, E>`的内部值。

- `Ok`变体 -> *panic*并输出默认错误信息
- `Err`变体 -> 返回`Err`中的默认值

签名如下：

```rust
impl<T, E> Result<T, E> {
    pub fn unwrap_err(self) -> E
    where
        T: Debug
}
```

代码示例：

```rust
let x: Result<u32, &str> = Ok(10);
x.unwrap_err(); // panic

let x: Result<u32, &str> = Err("failure");
assert_eq!(x.unwrap_err(), "failure");
```

#### unwrap_unchecked

`unwrap_unchecked`用来提取`Result<T, E>`的内部值。

> 当在`Err`上使用`unwrap_unchecked`时，会发生未定义行为。

#### unwrap_err_unchecked

`unwrap_err_unchecked`用来提取`Result<T, E>`的内部值。

> 当在`Ok`上使用`unwrap_unchecked`时，会发生未定义行为。

### 内部值转换

- [err](#err)
- [ok](#ok)
- [transpose](#transpose)
- [map](#map)
- [map_err](#map_err)
- [map_or](#map_or)
- [map_or_else](#map_or_else)

#### err

转换`Result<T, E>`为`Option<E>`

- `Ok`变体 -> 返回`None`
- `Err`变体 -> 返回`Some(E)`

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn err(self) -> Option<E>
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.err(), None);

let x: Result<u32, &str> = Err("Nothing here");
assert_eq!(x.err(), Some("Nothing here"));
```

#### ok

转换`Result<T, E>`为`Option<T>`

- `Ok`变体 -> 返回`Some(v)`
- `Err`变体 -> 返回`None`

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn ok(self) -> Option<T>
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
assert_eq!(x.ok(), Some(2));

let x: Result<u32, &str> = Err("Nothing");
assert_eq!(x.ok(), None);
```

#### transpose

转换一个`Result<Option<T>, E>`为`Option<Result<T, E>>`

- `Ok(Some(v))` 变体 -> 返回`Some(Ok(v))`
- `Ok(None)`变体 -> 返回`None`
- `Err` 变体 -> 返回`Some(Err(e))`

签名如下:

```rust
impl<T, E> Result<Option<T>, E> {
  pub fn transpose(self) -> Option<Result<T, E>>
}
```

代码示例:

```rust
#[derive(Debug, Eq, PartialEq)]
struct SomeErr;

let x: Result<Option<i32>, SomeErr> = Ok(Some(5));
let y: Option<Result<i32, SomeErr>> = Some(Ok(5));
assert_eq!(x.transpose(), y);
```

#### map

转换一个`Result<T, E>`为`Result<U, E>`

- `Ok`变体 -> 使用映射函数把类型为`T`的值转次为类型为`U`的值
- `Err`变体 -> 保持`Err`变体不变

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn map<U, F>(self, op: F) -> Result<U, E>
  where
    F: FnOnce(T) -> U
}
```

代码示例:

```rust
let line = "1\n2\n3\n4\n";

for num in line.linnes() {
  match num.parse::<i32>().map(|i| i*2) {
    Ok(n) => println!("{n}"),
    Err(..) => {}
  }
}
```

#### map_err

`map_err`用来转换一个`Result<T, E>`为`Result<T, O>`。

- `Ok`变体 -> 保持`Ok`变体不变
- `Err`变体 -> 使用映射函数把类型为`E`的值转次为类型为`O`的值

签名如下:

```rust
impl<T, E> for Result<T, E> {
  pub fn map_err<F, O>(self, op: F) -> Result<T, O>
    where F: FnOnce(E) -> O
}
```

代码示例：

```rust
fn stringify(x: u32) -> String { format!("error code: {x}")}

let x: Result<u32, u32> = Ok(2);
assert_eq!(x.map_err(stringify), Ok(2));

let x: Result<u32, u32> = Err(13);
assert_eq!(x.map_err(stringify), Err("error code: 13".to_string()));
```

#### map_or

`map_or`把`Result<T, E>`转换为`U`:

- `Ok`变体 -> 使用映射函数把类型为`T`的`Ok`变体转变为类型为`U`
- `Err`变体 -> 使用提供的类型为`U`的值作为返回值

签名如下:

```rust
impl<T, E> Result<T, E> {
 pub fn map_or<U, F>(self, default: U, op: F) -> U
   where F: FnOnce(T) -> U
}
```

代码示例:

```rust
let x: Result<_, &str> = Ok("foo");
assert_eq!(x.map_or(42, |v| v.len()), 3);

let x: Result<&str, _> = Err("bar");
assert_eq!(x.map_or(42, |v| v.len()), 42);
```

#### map_or_else

`map_or_else`把`Result<T, E>`转换为`U`:

- 变体`Ok` -> 使用映射函数把类型为`T`的`Ok`变体转变为类型为`U`的值
- 变体`Err` -> 使用提供的函数的返回值作为返回值

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn map_or_else<U, D, F>(self, default: D, op: F) -> U
    where
      D: FnOnce(E) -> U,
      F: FnOnce(T) -> U
}
```

### 布尔操作

和[Option](./option.md)一样，`Result`也有类似布尔的操作，也是分为两类，一类接收另一个`Result`作为参数，另一类接收一个返回`Result`的函数作为参数。同时，这类操作符也有短路操作。

接收`Result`作为参数：

|方法|self   |输入     |输出   |
|----|-------|---------|-------|
|and |Err(e) |(ignored)|Err(e) |
|and |Ok(x)  |Err(d)   |Err(d) |
|and |Ok(x)  |Ok(y)    |Ok(y)  |
|or  | Err(e)|Err(d)   |Err(d) |
|or  | Err(e)|Ok(y)    | Ok(y) |
|or  | Ok(x) |(ignored)| Ok(x) |

#### and

`and`用于操作和另一个`Result`的关系处理，其处理和布尔值操作中的`&&`是类似的:

- `Ok`变体 -> 返回操作数的内容
- `Err`变体 -> 不处理操作数，直接返回自身结果

签名如下:

```rust
impl<T, E> Result<T, E> {
  pub fn and<U>(self, other: Result<U, E>) -> Result<U, E>
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
let y: Result<u32, &str> = Err("late error");
assert_eq!(x.and(y), Err("late error"));

let x: Result<u32, &str> = Err("early error");
let y: Result<&str, &str> = Ok("foo");
assert_eq!(x.and(y), Err("early error"));

let x: Result<u32, &str> = Err("not a 2");
let y: Result<&str, &str> = Err("late error");
assert_eq!(x.and(y), Err("not a 2"));

let x: Result<u32, &str> = Ok(2);
let y: Result<&str, &str> = Ok("foo");
assert_eq!(x.and(y), Ok("foo"));
```

#### or

`or`用于操作或者另一个`Result`的关系处理，其处理或者布尔值操作中的`||`是类似的:

- `Ok`变体 -> 直接返回自身内容
- `Err`变体 -> 返回操作数的内容

签名如下:

```rust
impl<T, E> Result<T, E> {
    pub fn or<F>(self, other: Result<T, F>) -> Result<T, F>
}
```

代码示例:

```rust
let x: Result<u32, &str> = Ok(2);
let y: Result<u32, &str> = Err("late error");
assert_eq!(x.or(y), Ok(2));

let x: Result<u32, &str> = Err("early error");
let y: Result<u32, &str> = Ok(2);
assert_eq!(x.or(y), Ok(2));

let x: Result<u32, &str> = Err("not a 2");
let y: Result<u32, &str> = Err("late error");
assert_eq!(x.or(y), Err("late error"));

let x: Result<u32, &str> = Ok(2);
let y: Result<u32, &str> = Ok("foo");
assert_eq!(x.or(y), Ok(2));
```

接收函数返回值作为参数：

|方法    | self | 输入         | 输出          | 结果  |
|--------|------|--------------|---------------|-------|
|and_then|Err(e)|(not provided)|(not provided) | Err(e)|
|and_then|Ok(x) | x            | Err(d)        | Err(d)|
|and_then|Ok(x) | x            | Ok(y)         | Ok(y) |
|or_else |Err(e)| e            | Err(d)        | Err(d)|
|or_else |Err(e)| e            | Ok(y)         | Ok(y) |
|or_else |Ok(x) |(not provided)|(not evaluated)| Ok(x) |

#### and_then

`and_then`用于操作和另一个`Result`的关系处理，其处理和布尔值操作中的`&&`是类似的:

- `Ok`变体 -> 调用函数
- `Err`变体 -> 返回自身

签名如下:

```rust
impl<T, E> Result<T, E> {
    pub fn and_then(U, F)(self, op: F) -> Result<U, E>
    where 
      F: FnOnce(T) -> Result<U, E>
}
```

代码示例:

```rust
fn sq_then_to_string(x: u32) -> Result<String, &'static str> {
 x.checked_mul(x).map(|sq| sq.to_string()).ok_or("overflowed")
}

assert_eq!(Ok(2).and_then(sq_then_to_string), Ok("4".to_string()));
assert_eq!(Ok(1_000_000).and_then(sq_then_to_string), Err("overflowed"));
assert_eq!(Err("not a number").and_then(sq_then_to_string), Err("not a number"));
```

#### or_else

`or_else`用于操作或者另一个`Result`的关系处理，其处理或者布尔值操作中的`||`是类似的:

- `Ok`变体 -> 直接返回`Ok`内容
- `Err`变体 -> 调用函数，返回函数内容

签名如下:

```rust
impl<T, E> Result<T, E> {
    pub fn or_else<F, O>(self, op: O) -> Result<T, F>
    where
      O: FnOnce(E) -> Result<T, F>
}
```

代码示例:

```rust
fn sq(x: u32) -> Result<u32, u32> { Ok(x * x) }
fn err(x: u32) -> Result<u32, u32> { Err(x) }

assert_eq!(Ok(2).or_else(sq).or_else(err), Ok(2));
assert_eq!(Ok(2).or_else(err).or_else(sq), Ok(2));
assert_eq!(Err(3).or_else(sq).or_else(err), Ok(9));
assert_eq!(Err(3).or_else(err).or_else(err), Err(3));
```

### 比较操作符

在`Result<T, E>`中，如果`T`和`E`都实现了`PartialOrd`，那么`Result<T, E>`就可以使用比较运算符。

- `Err` \< `Ok`
- 如果两个值都是`Ok`或`Err`，那么就使用他们的内部值进行比较。

### 迭代器

- into_iter
- iter
- iter_mut

### 聚合

由于`Result`实现了`FromIterator`特性，这样就可以把一系列的`Result`聚合成一个`Result`，只要在这个系列中存在一个`Err`，那么最后都会聚合为`Err`。

示例代码：

```rust
let v = [Ok(2), Ok(4), Err("err!"), Ok(8)];
let res: Result<Vec<_>, &str> = v.into_iter().collect();
assert_eq!(res, Err("err!"));

let v= [Ok(2), Ok(4), Ok(8)];
let res: Result<Vec(_), &str> = v.into_iter().collect();
assert_eq!(res, Ok(vec![2, 4, 8]));
```

另外，由于`Result`实现了`Product`和`Sum`特性，所以可以使用`sum`和`product`方法：

代码示例：

```rust
let v = [Err("error!"), Ok(1), Ok(2), Ok(3), Err("foo")];
let res: Result<i32, &str> = v.into_iter().sum();
assert_eq!(res, Err("error!"));

let v = [Ok(1), Ok(2), Ok(3)];
let res: Result<i32, &str> = v.into_iter().sum();
let pro: Result<i32, &str> = v.into_iter().product();
assert_eq!(res, Ok(6));
assert_eq!(pro, Ok(6));
```
