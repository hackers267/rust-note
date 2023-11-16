# serde

## derive宏

在结构体或是枚举上，我们可以使用多个derive宏，部分宏如下：

* serderenamename
* serderenameall

### `#[serde(rename="name")]`

这个宏用于把结构体的`name`在序列化和反序列化的时候，重新命名为`name`值。在需要的使用，可以分别指定*序列化*和*反序列化*时的名字，或单独指定。

* `#[serde(rename(serialize = "ser_name"))]`
* `#[serde(rename(deserialize = "de_name))]`
* `#[serde(rename(serialize = "ser_name", deserialize = "de_name"))]`

### `#[serde(rename_all = "...")]`

根据下面中指定的规则其中之一，把所有的字段进行重命名:

* lowercase
* UPPERCASE
* PascalCase
* camelCase
* snake_case
* SCREAMING_SNAKE_CASE
* kebab-case
* SCREAMING-KEBAB-CASE

同样，也可以分别指定在*序列化*和*反序列化*时应用的规则，或单独指定：

* `#[serde(rename_all(serialize = "..."))]`
* `#[serde(rename_all(deserialize = "..."))]`
* `#[serde(rename_all(serialize = "...", deserialize = "..."))]`

## 自定义`Deserializer`

先上代码：
```rust
use chrono::{DateTime, Local, TimeZone};
use serde::de::{Error, Visitor};
use serde::Deserializer;
use std::fmt::Formatter;

#[derive(Deserialize)]
pub struct Example {
    #[serde(deserialize_with = "str_to_local_datetime")]
    pub time: Option<DateTime<Local>>,
}

struct StrVisitor;

impl<'de> Visitor<'de> for StrVisitor {
    type Value = String;

    fn expecting(&self, formatter: &mut Formatter) -> std::fmt::Result {
        formatter.write_str("Example Visitor")
    }

    fn visit_str<E>(self, v: &str) -> Result<Self::Value, E>
        where
            E: Error,
    {
        Ok(String::from(v))
    }
}

pub fn str_to_local_datetime<'de, D>(str: D) -> Result<Option<DateTime<Local>>, D::Error>
    where
        D: Deserializer<'de>,
{
    let result = str.deserialize_str(StrVisitor);
    result.map(|v| Local.datetime_from_str(&v, "%Y-%m-%dT%H:%M").ok())
}
```

如下代码，如果我们需要为某个字段自定义`deserializer`，就需要实现一个签名为`fn<'de, D>(D) -> Result<T, D::Error> where D: Deserializer<'de>`的函数。

在其中的`D`需要通过`Visitor`来把接收到的值转为我们需要的值。在示例中，我们打算把一个`str`转换为`Option<DataTime<Local>>`,所以我们要为`D`调用`deserializer_str`方法。而`deserializer_str`方法接收一个结构体，这个结构体要求实现一个`Visitor`的`trait`。在`Visitor`中，我们需要实现的是`visitor_str`方法和必要的`expecting`方法就可以。
