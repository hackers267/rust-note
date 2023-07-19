+++
title = "Option"
date = 2023-07-17
[taxonomies]
tags = ["std"]
[extra]
toc = true
+++


# Option

`Option`是 Rust 标准库里最常用的枚举体之一。Rust 利用`Option`消除了*Java*等语言中臭名昭著的空指针异常的问题。`Option`
类型代表了一个可选值，这个值要么是`Some<T>`，要么是`None`。`Some<T>`指的是存在一个值，但这个值包含在`Some`枚举体中，而`None`
代表了值不存在。

`Option`在 Rust 中可能使用在以下的场景中：

- 一个初始值
- 一个函数的返回值，当返回`None`时代表无关紧要的错误
- 可选的结构体字段
- 可借用的结构体字段
- 可选的函数参数
- '空指针'

## 保证

在 Rust 中使用`Option`的时候，Rust 可以保证使用`Option<T>`和使用`T`在下面的情况下的类型大小是一样的：

- Box\<U>
- &U
- &mut U
- fn, extern "C" fn
- num::NoneZero\*
- ptr::NonNull\<U>
- #\[repr(transparent)\] struct around one of the types in this list

## Option和`?`操作符

和`Result`一样，`Option`也可以通过`?`操作符来精简代码。示例如下：

原代码：

```rust
fn add_last_numbers(stack: &mut Vec<i32>) -> Option<i32> {
 let a = stack.pop();
 let b = stack.pop();

 match(a,b) {
    (Some(x), Some(y)) => Some(x+y),
    _ => None,
 }
}
```

精简代码：
```rust
fn add_last_numbers(stack: &mut Vec<i32>) -> Option<i32> {
  Some(stack.pop()? + stack.pop()?)
}
```

## 方法概览

### 判断

- is_some 判断`Option`是不是一个`Some`值
- is_none 判断`Option`是不是一个`None`值
- is_some_and 判断`Option`是不是一个`Some`值并且符合给定的断言

#### is_some

`is_some`方法用于判断`Option`是不是一个`Some`值，是`Some`值，返回`true`，不是`Some`值，返回`false`;

签名如下：

```rust
fn is_some(&self) -> bool
```

代码示例：

```rust
let x: Option<u32> = Some(2);
assert_eq!(x.is_some(), true);

let x: Option<u32> = None;
assert_eq!(x.is_some(), false);
```

#### is_none

`is_none`方法用于判断`Option`是不是一个`None`值，是`None`返回`true`，否则返回`false`;

签名如下：

```rust
fn is_none(&self) -> bool
```

代码示例：

```rust!
let x:Option<u32> = Some(2);
assert_eq!(x.is_none(), false);

let x:Option<u32> = None;
assert_eq!(x.is_none(), true);
```

#### is_some_and

`is_some_and`方法在同时满足以下两个条件时返回`true`：

- `Option`是一个`Some`值
- `Some`的内容值满足给定的断言函数。

否则返回`false`。

签名如下：

```rust
fn is_some_and(self, f: F) -> bool
    where F: FnOnce(T) -> bool
```

代码示例：

```rust
fn more_than_one(n: u32) -> bool {
    n > 1
}

let x: Option<u32> = Some(2);
assert_eq!(x.is_some_and(more_than_one), true);

let x: Option<u32> = Some(0);
assert_eq!(x.is_some_and(more_than_one), false);

let x: Option<u32> = None;
assert_eq!(x.is_some_and(more_than_one), false);
```

### 转换

- as_ref 把 *&Option<T>* 转换为 *Option<&T>*
- as_mut 把 *&mut Option<T>* 转换为*Option<&mut T>*
- as_deref 把 *&Option<T>* 转换为*Option<&T::Target>*
- as_deref_mut 把 *&mut Option<T>* 转换为*Option<&mut T::Target>*
- as_pin_ref 把 *Pin<&Option<T>>* 转换为*Option<Pin<&T>>*
- as_pin_mut 把 *Pin<&mut Option<T>>* 转换为*Option<Pin<&mut T>>*
- cloned 通过**clone**方法把 *Option<&T>* 或 *Option<&mut T>* 转换为*Option<T>*
- copied 通过**copy**方法把 *Option<&T>* 或 *Option<&mut T>* 转换为*Option<T>*

#### as_ref

`as_ref`方法转换`&Option<T>`为`Option<&T>`，其主要的作用是可以把通过这个转换保留`T`的所有权，方便在之后使用。

签名如下：

```rust
fn as_ref(&self) -> Option<&T>
```

示例代码：

```rust
let text:Option<String> = Some("Hello, rust!".to_string());
let text_lenghth: Option<uszie> = text.as_ref().map( | s| s.len());
println!("still can print text: {text:?}");
```

#### as_mut

`as_mut`把`&mut Option<T>`转换为`Option<&mut T>`;

签名如下：

```rust
fn as_mut(&mut self) -> Option<&mut T>
```

示例代码：

```rust
let mut x = Some(2);
match x.as_mut() {
Some(v) => * v = 42,
None => {},
}
assert_eq!(x, Some(42));
```

#### as_deref

`as_deref`转换`Option<T>`或`&Option<T>`为`Option<&T::Target>`;

签名如下：

```rust
fn as_deref(&self) -> Option<&<T as Deref>::Target>
    where
        T: Deref
```

示例代码：

```rust
let x: Option<String> = Some("hey".to_owned());
assert_eq!(x.as_deref(), Some("hey"));

let x: Option<String> = None;
assert_eq!(x.as_deref(), None);
```

#### as_deref_mut

`as_deref_mut`转换`Option<T>`或`&mut Option<T>`为`Option<&mut T::Target>`;

签名如下：

```rust
fn as_deref_mut(&mut self) -> Option<&<T as Deref>::Target>
    where
        T: DerefMut
```

示例代码：

```rust
let mut x: Option<String> = Some("hey".to_owned());
assert_eq!(x.as_deref_mut().map(|x| {
    x.make_ascii_uppercase();
    x
  }), Some("HEY".to_owned().as_mut_str());
```

#### as_pin_ref

`as_pin_ref`转换`Pin<&Option<T>>`为`Option<Pin<&T>`

#### as_pin_mut

`as_pin_mut`转换`Pin<&mut Option<T>>` 为`Option<Pin<&mut T>>`;

#### cloned

通过**clone**Option 中的内容，把`Option<&T>`或`Option<&mut T>`转换为`Option<T>`;

#### copied

通过**copy**Option 中的内容，把`Option<&T>`或`Option<&mut T>`转换为`Option<T>*`;

### 提取值

- expect 提取`Option`中的值，如果是`None`，则会**panic**，并输出自定义的信息
- unwrap 提取`Option`中的值，如果是`None`，则会**panic**,并输出通过信息
- unwrap_or 提取`Option`中的值，如果是`None`，则返回提供的*默认值*
- unwrap_or_default 提取`Option`中的值，如果是`None`，则返回类型的*默认值*
- unwrap_or_else 提取`Option`中的值，如果是`None`，则返回提供的函数执行后的返回值

#### expect

`expect`方法是把`Option`对内部值的包装除去，也就是说取出内部值。如果`Option`是一个`Some`，那么返回内部值；如果`Option`是`None`，
那么就`panic`自定义的错误信息。

签名如下：

```rust
fn expect(self, msg: &str) -> T
```

代码示例：

```rust
let x = Some("value");
assert_eq!(x.expect("fruits are healthy"), "value");

let y: Option<&str> = None;
x.expect("fruits are healthy"); // panics with `fruits are healthy`
```

#### unwrap

`unwrap`方法和`expect`方法一样，都是取出`Option`中的内部值。如果`Option`是`Some`，那么返回内部值；如果`Option`是`None`,那么就`panic`默认的错误信息。

签名如下：

```rust
fn unwrap(self) -> T
```

代码示例：

```rust
let x = Some("air");
assert_eq!(x.unwrap(), "air");

let x: Option<&str> = None;
assert_eq!(x.unwrap(), "air"); // fails
```

#### unwrap_or

`unwrap_or`方法也是从`Option`取出内部值，但是`unwrap_or`方法可以提供一个默认值，当`Option`是`None`的时候，取出的内部值会是提供的默认值。

签名如下：
```rust
fn unwrap_or(self, default: T) -> T
```

示例代码：
```rust
assert_eq!(Some("car").unwrap_or("bike"), "car");
assert_eq!(None.unwrap_or("bike"), "bike");
```

#### unwrap_or_else

`unwrap_or_else`方法同样是从`Option`中取出内部值，但是`unwrap_or_else`方法提供了一个返回默认值的函数作为参数，当`Option`是`None`时，会返回函数的返回值，而不是`panic`。

签名如下：
```rust
fn unwrap_or_else(self, f:F) ->
where
  F: FnOnce() -> T
```

示例代码：
```rust
let k = 10;
assert_eq!(Some(4).unwrap_or_else(|| 2*k), 4);
assert_eq!(None.unwrap_or_else(|| 2*k), 20);
```

#### unwrap_or_default

`unwrap_or_default`方法同样是从`Option`取出内部值。但是当`Option`是`None`，其返回值为内部值的默认值，如内部值的类型为`String`，返回`String::default()`，
内部值的类型为`u8`，返回`0`。

签名如下：
```rust
fn unwrap_or_default(self) -> T
```

示例代码：
```rust
let x: Option<u32> = None;
let y: Option<u32> = Some(12);

assert_eq!(x.unwrap_or_default(), 0);
assert_eq!(y.unwrap_or_default(), 12);
```

### 和**Result**之间的转换

- ok_or 如果`Option`是`Some(v)`则转换为`Ok(v)`，如果`Option`是`None`，则使用提供的默认`err`值转换为`Err(err)`
- ok_or_else 如果`Option`是`Some(v)`则转换为`Ok(v)`，如果`Option`是`None`，则使用提供的函数的返回值转换为`Err(err)`
- transpose 把`Option<Result<T,E>>`转换为`Result<Option<T>,E>`

#### ok_or

`ok_or`提供了一个把`Option`转换为`Result`的方法：

- 如果是`Some(v)`，则转换成`Ok(v)`
- 如果是`None`，则转换成`Err(err)`

签名如下：
```rust
let x = Some("foo");
assert_eq!(x.ok_or(0), Ok("foo"));

let x: Option<&str> = None;
assert_eq!(x.ok_or(0), Err(0));
```

#### ok_or_else

`ok_or_else`也是一种把`Option`转换为`Result`的方式，其提供了一个函数，这个函数可以在`Option`为`None`，把返回值作为`Err`的值：

- 如果是`Some(v)`，则转换成`Ok(v)`
- 如果是`None`，则把函数的返回值作为值，转换成`Err(err)`

签名如下：
```rust
fn ok_or_else(self,f: F) -> Result<T, E>
where
  F: FnOnce() -> E
```

示例代码：
```rust
let x = Some("foo");
assert_eq!(x.ok_or_else(|| 0), Ok("foo"));

let y: Option<&str> = None;
assert_eq!(x.ok_or_else(|| 0), Err(0));
```

#### transpose

`transpose`的作用就是把`Option<Result<T,E>`转换成`Result<Option<T>,E>`的形式。

- `None` -> `Ok(None)`
- `Some(Ok(_))` -> `Ok(Some(_))`
- `Some(Err(_))` -> `Err(_)`

签名如下：
```rust
impl<T,E> Option<Result<T, E>> {
    fn transpose(self) -> Result<Option<T>, E>
}
```

示例代码：
```rust
#[derive(Debug, Eq, PartialEq)]
struct SomeErr;

let x: Result<Option<i32>, SomeErr> = Ok(Some(5));
let y: Option<Result<i32, SomeErr>> = Some(Ok(5));
assert_eq!(y.transpose(), x);
```

### 其它转换

- filter
- flatten
- map
- map_or
- map_or_else
- zip
- zip_with
- unzip

#### filter

`filter`方法的主要作用是作用在某些`Some`值上，它可以把某些符合条件的`Some`值保留下来，其他的值都转换为`None`值。

`filter`方法接收一个断言闭包或函数作为参数，这个闭包接收`Some(v)`中的`v`作为参数并返回一个布尔值。如果断言返回
true,那么`Some(v)`保留，否则返回`None`.

签名：

```rust
fn filter<P>(self, predicate: P) -> Option<T>
    where P: FnOnce(&T) -> bool,
```

示例：

```rust
fn is_even(n: &i32) -> bool {
    n % 2 == 0
}

assert_eq!(None.filter(is_even), None);
assert_eq!(Some(1).filter(is_even), None);
assert_eq!(Some(2).filter(is_even), Some(2));
```

#### flatten

`flatten`方法的作用是打平`Option`，即把`Option<Option<T>>`转换为`Option<T>`;

签名：

```rust
fn flatten(self) -> Option<T>
```

示例:

```rust
let x: Option<Option<u32> > = Some(Some(6));
assert_eq!(x.flatten(), Some(6));

let x: Option<Option<u32> = Some(None);
assert_eq!(x.flatten(), None);

let x: Option<Option<u32> > = None;
assert_eq!(x.flatten(), None);
```

#### map

`map`方法的作用是把`Option<T>`转换为`Option<U>`，即通过闭包或函数把`T`类型转换为`U`类型。如果`Option`是`Some`
,那么会使用闭包或函数对`Some`中的值进行转换，如果`Option`是`None`，则保持`None`不变。

签名：

```rust
fn map<U, F>(self, f: F) -> Option<U>
    where F: FnOnce(T) -> U
```

示例代码：

```rust
let maybe_some_string = Some(String::from("Hello, World!"));
let maybe_some_len = maybe_some_string.map( | s| s.len());
assert_eq!(maybe_some_len, Some(13));

let x:Option< & str> = None;
assert_eq!(x.map(|s| s.len()), None);
```

#### map_or

`map_or`方法是把`Option<T>`转换为`U`类型，`map_or`依然会使用闭包或函数把`Some<T>`转为`U`，但如果`Option`是`None`，`map_or`
会返回提供的默认值。

签名如下：

```rust
fn map_or<U, F>(self, default: U, f: F) -> U
    where F: FnOnce(T) -> U
```

代码示例：

```rust
let x = Some("foo");
assert_eq!(x.map_or(42, |v| v.len()), 3);

let x = None;
assert_eq!(x.map_or(42, |v| v.len()), 42);
```

#### map_or_else

`map_or_else`方法同样是把`Option<T>`转换为`U`类型，其处理`Some`的方法和`map_or`是一致的，不同之处在于处理`None`
值时，`map_or`提供的是一个默认值，而`map_or_else`提供的是一个执行函数，当`Option`是`None`
值，其执行函数的返回值会作为`map_or_else`的返回值。

签名如下：

```rust
fn map_or_else<U, D, F>(self, default: D, f: F) -> U
    where
        D: FnOnce() -> U,
        F: FnOnce(T) -> U,
```

示例代码：

```rust
let k = 21;
let x = Some("foo");
assert_eq!(x.map_or_else(|| 2 * k, |v| v.len()), 3);

let x = None;
assert_eq!(x.map_or_else(|| 2 * k, |v| v.len()), 42);
```

#### zip

`zip`方法用于合并两个`Option`，把`Option<T>`和`Option<U>`合并为`Option<(T,U)>`。如果`Option<T>`和`Option<U>`同时为`Some`
，则返回`Some<(T,U)>`，否则返回`None`。

签名如下：

```rust
fn zip<U>(self, other: Option<U>) -> Option<(T, U)>
```

示例代码：

```rust
let x = Some(1);
let y = Some("foo");
let z = None: < u8>;

assert_eq!(x.zip(y), Some((1, "foo")));
assert_eq!(x.zip(z), None);
```

#### unzip

`unzip`是`zip`的逆操作，就是把形式如`Option<(T,U)>`的二元元组 Option 拆分为`(Option<T>,Option<U>)`的二元元组。
如果`Option`的值为`Some((T,U))`，则返回`(Some(T),Some(U))`，否则返回`(None, None)`.

签名如下：

```rust
fn unzip(self) -> (Option<T>,Option<U>)
```

代码示例：

```rust
let x = Some((1,"hi"));
let y = None::<(u8,u32)>;

assert_eq!(x.unzip(), (Some(1), Some("hi")));
assert_eq!(y.unzip(), (None, None));
```

### 布尔操作

在`Option`中有一类操作符被称之为布尔操作符，这些操作符把`Some`视为`true`，把`None`视为`false`。而这类操作符根据接收的参数，又可以分为
2 类，一类直接接收一个`Option`作为参数，另一类接收一个返回值为`Option`的函数作为参数。

接收`Option`作为参数的一类：

| 方法 | self    | 输人      | 结果    |
| ---- | ------- | --------- | ------- |
| and  | None    | (ignored) | None    |
| and  | Some(x) | None      | None    |
| and  | Some(x) | Some(y)   | Some(y) |
| or   | None    | None      | None    |
| or   | None    | Some(x)   | Some(x) |
| or   | Some(x) | (ignored) | Some(x) |
| xor  | None    | None      | None    |
| xor  | None    | Some(x)   | Some(x) |
| xor  | Some(x) | None      | Some(x) |
| xor  | Some(x) | Some(y)   | None    |

#### and

`and`方法作用于两个`Option`之间，当且仅当两个`Option`都为`Some`时，返回连接的`Some`，否则返回`None`。其应用场景是：

- 当两个`Option`都为`Some`时，需要取其中一个`Some`的值。

签名如下：

```rust
fn and<U>(self, optb: Option<U>) -> Option<U>
```

示例代码：

```rust
let x = Some(2);
let y: Option< & str> = None;
assert_eq!(x.and(y), None);

let x: Option<u32> = None;
let y = Some("foo");
assert_eq!(x.and(y), None);

let x = Some(2);
let y = Some("foo");
assert_eq!(x.and(y), Some("foo"));

let x: Option<u32> = None;
let y: Option< & str> = None;
assert_eq!(x.and(y), None);
```

#### or

`or`方法作用于两个`Option`之间，当且仅当两个`Option`都为`None`时，返回`None`，否则返回首先出现的`Some`。其应用场景为：

- 为某个可能为`None`的值提供一个默认值。

签名如下：

```rust
fn or(self, optb: Option<T>) -> Option<T>
```

代码示例：

```rust
let x = Some(2);
let y = None;
assert_eq!(x.or(y), Some(2));

let x = None;
let y = Some(100);
assert_eq!(x.or(y), Some(100));

let x = Some(2);
let y = Some(100);
assert_eq!(x.or(y), Some(2));

let x: Option<u32> = None;
let y = None;
assert_eq!(x.or(y), None);
```

#### xor

`xor`方法同样作用下再个`Option`之间，当两个`Option`同时为`Some`或同时为`None`时，返回`None`。否则返回其中的一个`Some`。

签名如下：

```rust
fn xor(self, optb: Option<T>) -> Option<T>
```

代码示例：

```rust
let x = Some(2);
let y: Option<u32> = None;
assert_eq!(x.xor(y), Some(2));

let x: Option<u32> = None;
let y = Some(2);
assert_eq!(x.xor(y), Some(2));

let x = Some(2);
let y = Some(2);
assert_eq!(x.xor(y), None);

let x: Option<u32> = None;
let y: Option<u32> = None;
assert_eq!(x.xor(y), None);
```

接收函数的返回值为`Option`的一类：

| 方法     | self    | 函数输入       | 函数输出        | 结果    |
| -------- | ------- | -------------- | --------------- | ------- |
| and_then | None    | (not provided) | (not evaluated) | None    |
| and_then | Some(x) | x              | None            | None    |
| and_then | Some(x) | x              | Some(y)         | Some(y) |
| or_else  | None    | (not provided) | None            | None    |
| or_else  | None    | (not provided) | Some(y)         | Some(y) |
| or_else  | Some(x) | (not provided) | (not evaluated) | Some(x) |

#### and_then

`and_then`方法使用一个返回值为`Option`的函数作为参数，其特点是：

- 如果`Option`是`None`，则返回`None`
- 如果`Option`是`Some`，则返回函数的返回值。

签名如下：

```rust
fn and_then(U, F)( self , f: F) -> Option<U>
where F: FnOnce(T) -> Option<U>,
```

代码示例：

```rust
fn sq_then_to_string(x: u32) -> Option<String> {
    x.checked_mul(x).map(|sq| sq.to_string())
}

assert_eq!(Some(2).and_then(sq_then_to_string), Some(4.to_string()));
assert_eq!(Some(1_000_1000).and_then(sq_then_to_string), None);
assert_eq!(None.and_then(sq_then_to_string), None);
```

#### or_else

`or_else`方法使用一个返回值为`Option`的函数作为参数，其特点是：

- 如果`Option`是`Some`，直接返回`Some`
- 如果`Option`是`None`，则返回函数的返回值。

签名如下：

```rust
fn or_else(self, f: F) -> Option<T>
    where
        F: FnOnce() -> Option<T>
```

代码示例：

```rust
fn nobody() -> Option<&'static str> { None }

fn tim() -> Option<&'static str> { Some("Tim") }

assert_eq!(Some("Jane").or_else(tim), Some("Jane"));
assert_eq!(None.or_else(tim), Some("Tim"));
assert_eq!(None.or_else(nobody), None);
```

#### insert

`insert`方法向`Option`中插入一个值，并返回一个指向这个值的可变引用。如果`Option`中已经包含值，那么旧值会被消除。

签名如下:

```rust
fn insert(&mut self, value:T) -> &mut T
```

示例代码：

```rust
let mut opt = None;
let val = opt.insert(1);
assert_eq!(*val, 1);
assert_eq!(opt.unwrap(), 1);
let val = opt.insert(2);
assert_eq!(*val, 2);
assert_eq!(opt.unwrap(), 2);
*val = 3;
assert_eq!(opt.unwrap(), 3);
```

> 在`Option`中有一个`get_or_insert`方法，其在`Option`中已经包含值时，不会更新`Option`。

#### get_or_insert

`get_or_insert`在`Option`是`None`时，向`Option`中插入值，然后返回`Option`中包含值的可变引用。

签名如下：

```rust
fn get_or_insert(&mut self, value:T) -> &mut T
```

示例代码：

```rust
let mut x = None;
{
    let y: mut u32 = x.get_or_insert(5);
    assert_eq!(y, &5);
    *y = 7;
}

assert_eq!(x, Some(7))
```

#### get_or_insert_with

如果`Option`是`None`，则使用函数的返回值插入到`Option`中，并返回值的可变引用。

签名如下：

```rust
fn get_or_insert_with<F>(&mut self,f: F) -> &mut T
where
  F: FnOnce() -> T,
```

代码示例：

```rust
let mut x = None;
{
  let y: &mut u32 = x.get_or_insert_with(|| 5);
  assert_eq!(y, &5);
  *y = 7;
}

assert_eq!(x, Some(7));
```

#### take

`take`方法取出`Option`中值，留下`None`值。

签名如下：

```rust
fn take(&mut self) -> Option<T>
```

示例代码：

```rust
let mut x = Some(2);
let y = x.take();
assert_eq!(y, Some(2));
assert_eq!(x, None);

let mut x: Option<u32> = None;
let y = x.take();
assert_eq!(y, None);
assert_eq!(x, None);
```

#### iter

`iter`返回一个内部值的迭代器。

签名如下：
```rust
fn iter(&self) -> Iter<'_, T>
```

代码示例：
```rust
let x = Some(4);
assert_eq!(x.iter().next(), Some(&4));

let x: Option<u32> = None;
assert_eq!(x.iter().next(), None);
```

#### iter_mut

`iter`返回一个可变引用的迭代器。

签名如下：
```rust
fn iter_mut(&mut self) -> IterMut<'_, T>
```

示例代码：
```rust
let mut x = Some(4);
match x.iter_mut().next() {
    Some() => *v = 42,
    None() => {},
}
assert_eq!(x, Some(42));

let mut x: Option<u32> = None();
assert_eq!(x.iter_mut().next(), None);
```
