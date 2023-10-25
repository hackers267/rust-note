# Rename

在不同的语言中，对于变量和字段的命令偏好不一样，在有的语言里偏好使用`camelCase`形式的命名(比如`javascript`)，有的语言里偏好使用`snake_case`形式的命名(比如`rust`)。在序列化和反反序列化的时候，`serde`为我们提供的方便的转换方法。

## 转换命名

可以使用`#serde[rename = "name"]`来对`struct`或`enum`进行名称转换。通过这个宏，我们就可以不用修改Rust的命名来适配不同的名称了。其类似的宏还有以下几个：

- #[serde(rename(serialize = "ser_name"))]
- #[serde(rename(deserialize = "de_name"))]
- #[serde(rename(serialize = "ser_name", deserialize = "de_name"))]

## 转换内容字段

### 全部转换

在通常的情况下，某个`struct`或`enum`的命名风格都是一致的，所以我们一般只需要统一转换就可以了。在这个使用，我们可以在`struct`或`enum`上使用`#[serde(rename_all="...")]`的宏即可。这个宏用于在序列化和反序列化的时候对内部的字段进行重命名。除此之外，还有以下方式：

- #[serde(rename_all(serialize = "..."))]
- #[serde(rename_all(deserialize = "..." ))]
- #[serde(rename_all(serialize = "...", deserialize = "..."))]

其命名可以使用如下字段：

- lowercase
- UPPERCASE
- PascalCase
- camelCase
- snake_case
- SCREAMING_SNAKE_CASE
- kebab-case
- SCREAMING-KEBAB-CASE

### 只转换部分字段

如果我们不需要把`enum`或`struct`中的字段全部转换或都是有部分字段的转换和其他的字段的悈中不一样，那我们就可以在字段上使用`#[serde(rename="...")]`,类似的宏有:

- #[serde(rename(serialize = "ser_name"))]
- #[serde(rename(deserialize = "de_name"))]
- #[serde(rename(serialize = "ser_name", deserialize = "de_name"))]

在转换内容字段的宏中，除了使用`rename`相应的宏外，还有另外一个比较有害的宏：`alias`。
#[serde(alias = "name")] 宏的作用是，在反序列化的时候，这个字段的值可以从`alias`指定的字段或`rust`的字段名中取值。

