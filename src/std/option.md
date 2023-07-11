# Option

`Option`是 Rust 标准库里最常用的枚举体之一。Rust 利用`Option`消除了*Java*等语言中臭名昭著的空指针异常的问题。`Option`类型代表了一个可选值，这个值要么是`Some<T>`，要么是`None`。`Some<T>`指的是存在一个值，但这个值包含在`Some`枚举体中，而`None`代表了值不存在。

`Option`在 Rust 中可能使用在以下的场景中：

- 一个初始值
- 一个函数的返回值，当返回`None`时代表无关紧要的错误
- 可选的结构体字段
- 可借用的结构体字段
- 可选的函数参数
- '空指针'

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
