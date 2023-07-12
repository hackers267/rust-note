# Option

`Option`是 Rust 标准库里最常用的枚举体之一。Rust 利用`Option`消除了*Java*等语言中臭名昭著的空指针异常的问题。`Option`类型代表了一个可选值，这个值要么是`Some<T>`，要么是`None`。`Some<T>`指的是存在一个值，但这个值包含在`Some`枚举体中，而`None`代表了值不存在。

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

## 方法概览

### 判断

- is_some 判断`Option`是不是一个`Some`值
- is_none 判断`Option`是不是一个`None`值

### 转换

- as_ref 把 *&Option<T>*转换为*Option\<&T>*
- as*mut 把 *&mut Option<T>\_ 转换为*Option\<&mut T>*
- as*deref 把 *&Option<T>\_ 转换为*Option\<&T::Target>*
- as*deref_mut 把 *&mut Option<T>\_ 转换为*Option\<&mut T::Target>*
- as*pin_ref 把 \_Pin\<&Option<T>>* 转换为*Option\<Pin\<&T>>*
- as*pin_mut 把 \_Pin\<&mut Option<T>>* 转换为*Option\<Pin\<&mut T>>*

### 提取值

- expect 提取`Option`中的值，如果是`None`，则会**panic**，并输出自定义的信息
- unwrap 提取`Option`中的值，如果是`None`，则会**panic**,并输出通过信息
- unwrap_or 提取`Option`中的值，如果是`None`，则返回提供的*默认值*
- unwrap_or_default 提取`Option`中的值，如果是`None`，则返回类型的*默认值*
- unwrap_or_else 提取`Option`中的值，如果是`None`，则返回提供的函数执行后的返回值

### 和**Result**之间的转换

- ok_or 如果`Option`是`Some(v)`则转换为`Ok(v)`，如果`Option`是`None`，则使用提供的默认`err`值转换为`Err(err)`
- ok_or_else 如果`Option`是`Some(v)`则转换为`Ok(v)`，如果`Option`是`None`，则使用提供的函数的返回值转换为`Err(err)`
- transpose 把`Option<Result<T,E>>`转换为`Result<Option<T>,E>`

### 其它转换

- filter
- flatten
- map
- map_or
- map_or_else
- zip
- zip_with

#### filter

`filter`方法的主要作用是作用在某些`Some`值上，它可以把某些符合条件的`Some`值保留下来，其他的值都转换为`None`值。

`filter`方法接收一个断言闭包或函数作为参数，这个闭包接收`Some(v)`中的`v`作为参数并返回一个布尔值。如果断言返回 true,那么`Some(v)`保留，否则返回`None`.

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

assert_eq!(None.filter(is_even),None);
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
let x: Option<Option<u32>> = Some(Some(6));
assert_eq!(x.flatten(), Some(6));

let x: Option<Option<u32> = Some(None);
assert_eq!(x.flatten(), None);

let x: Option<Option<u32>> = None;
assert_eq!(x.flatten(), None);
```

#### map

`map`方法的作用是把`Option<T>`转换为`Option<U>`，即通过闭包或函数把`T`类型转换为`U`类型。如果`Option`是`Some`,那么会使用闭包或函数对`Some`中的值进行转换，如果`Option`是`None`，则保持`None`不变。

签名：

```rust
fn map<U,F>(self, f:F) -> Option<U>
  where F: FnOnce(T) -> U
```

示例代码：

```rust
let maybe_some_string = Some(String::from("Hello, World!"));
let maybe_some_len = maybe_some_string.map(|s| s.len());
assert_eq!(maybe_some_len, Some(13));

let x:Option<&str> = None;
assert_eq!(x.map(|s| s.len()), None);
```

#### map_or

`map_or`方法是把`Option<T>`转换为`U`类型，`map_or`依然会使用闭包或函数把`Some<T>`转为`U`，但如果`Option`是`None`，`map_or`会返回提供的默认值。

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

`map_or_else`方法同样是把`Option<T>`转换为`U`类型，其处理`Some`的方法和`map_or`是一致的，不同之处在于处理`None`值时，`map_or`提供的是一个默认值，而`map_or_else`提供的是一个执行函数，当`Option`是`None`值，其执行函数的返回值会作为`map_or_else`的返回值。

签名如下：

```rust
fn map_or_else<U, D, F>(self,default: D, f: F) -> U
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

`zip`方法用于合并两个`Option`，把`Option<T>`和`Option<U>`合并为`Option<(T,U)>`。如果`Option<T>`和`Option<U>`同时为`Some`，则返回`Some<(T,U)>`，否则返回`None`。

签名如下：

```rust
fn zip<U>(self, other: Option<U>) -> Option<(T, U)>
```

示例代码：

```rust
let x = Some(1);
let y = Some("foo");
let z = None:<u8>;

assert_eq!(x.zip(y), Some((1, "foo")));
assert_eq!(x.zip(z), None);
```
