# enum

`enum`是`rust`中的主要类型之一， 其为`rust`提供了一个强大的能力，可以为我们把不同的分支逻辑处理的更加有条理。`enum`相比下其他语言的枚举有更好的表现力，所以，在合适的情况下正确使用`enum`可以请我们代码更有表现力，逻辑更加清晰。在使用枚举进行交互的时候，就有在和其他数据进行转换的时候，我们如何把一个数据转换为枚举，又如何把枚举转为其他语言的数据，比如和`json`之间进行转换。

这个时候，我们就可以使用`serde`中对枚举的支持了。

## `serde`对`enum`的4种标记

`serde`为了可以更好的在序列化和反序列化的时候对`enum`的支持。其提供了4种标记功能，可以使我们在不同的情况下对数据进行转换：

- 外部标记(默认)
- 内部标记
- 邻近标记
- 无标记

### 外部标记

在默认的情况下，`serde`对于`enum`进行的是*外部标记*的推断。比如，对于下面的`enum`:

```rust
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize)]
enum Message {
    Request { id: String, method: String, params: Params },
    Response { id: String, result: Value}
}
```

因为没有对`enum`进行标记处理，所以其是外部标记。`serde`会把`Message::Request`序列为以下的形式:`{"Request": { "id": "...", "method": "...", "params": {}}}`

同时，如果遇到上面形式的`json`数据，也会反序列化为`Message::Request`类型。

### 内部标记

如果在`json`中的某个字段明确指明了`enum`的类型值，或者在把`enum`转换为`json`时，需要明确其类型，可以使用*内容标记*的方式。再用上面的`enum`为例:

```rust
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize)]
#[serde(tag = "type")]
enum Message {
    Request { id: String, method: String, params: Params },
    Response { id: String, result: Value}
}
```

内部标记和外部标记的区别在于其通过`#[serde(tag = "type")]`中方式提明了需要在序列化和反序列化时考虑`type`字段，在序列化时，需要`enum`的值转换为对应的`type`字段的值。如需要把`Message::Request`转为`{"type": "Request""}`,在反反序列化时，需要查报`type`字段转换为对应的`enum`。上面的一个示例就是

```json
{
  "type": "Request",
  "id": "...",
  "method": "...",
  "params": {}
}
```

### 无标记

无标记可以是在和其他的语言交互中使用比较多的另一种标记，其可以根据具体字段来进行序列化和反序列化。比如：

```rust

use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize)]
#[serde(untagged)]
enum Message {
    Request { id: String, method: String, params: Params },
    Response { id: String, result: Value}
}
```

上面的代码会和这样的`json`进行序列化和反序列化转换：

```json
{
  "id": "...",
  "method": "...",
  "params": {}
}
```

无标记会忽略`enum`中的枚举值字段，尝试着把其内部的字段逐一匹配，最后转换为最匹配的形式。

### 邻近标记

*邻近标记*这个词翻译的可能不太准确，其主要是通过两个标记字段来序列化和反序列化`enum`，其中一个用于标记类型，另外一个用于标记内容。比如：

```rust

use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize)]
#[serde(tag = "t", content = "c")]
enum Block {
    Para(Vec<Inline>),
    Str(String)
}
```

其对应的`json`形式如下：

```json
{
  "t": "Para",
  "c": []
}
```
```json
{
  "t": "Str",
  "c": "the string"
}
```
