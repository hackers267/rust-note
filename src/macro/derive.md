# 派生宏

派生宏是`rust`中最常用的过程宏之一。当我们需要使用派生宏的时候，我们还有需要使用到两个库`quote`和`syn`。

## syn

`syn`主要是一个解析库，用于把Rust标记流解析为Rest源代码的语法树。目前该库面向过程宏。

### DeriveInput 数据结构

`DeriveInput`类型包含有下面几个字段：

- attrs: Vec<Attribute> 属性列表
- vis: Visibility 可见性，比如 pub
- ident: Ident 标记符 比如 struct的名字
- generics: Generics 泛型标记
- data: Data 结构体或枚举或元组的内容

我们可以从`DeriveInput`获取到一个结构体或枚举或单元体的可见性,属性，名字，泛型和结构体等。

### Data

在`DeriveInput`中，`Data`是一个枚举，其值分别代表：

- Struct(DataStruct) 结构体内容
- Enum(DataEnum) 枚举内容
- Uniom(DataUnion) 元组内容

#### DataStruct

`DataStruct`是一个结构体，其中包含有三个字段，分别是:

- struck_token: 表示一个struct的token,
- fields: (Fields类型)表示的是内容的字段
- semi_token: 表示一个 分号字段的token

其中的`fields`是我们使用的时候比较关注的内容，其代表我们在书写结构体时包含的内容。

`Fields`同样是一个枚举，其值分别是：

- Named(FieldsNamed): 表示一个已命令的结构体，比如`Point { x: f64, y: 64 }`
- Unnamed(FieldsUnnamed): 表示一个没有命令的元组结构体或元组变体的未命名字段，比如`Some(T)`
- Unit: 表示一个单元体，比如`None`

`FieldsNamed`是一个结构体字段，其两个字段:

- brace_token: token::Barce 表明使用`Brace`(`{}`)包裹
- named: 具体内容字段，是一个`Punctuated`

`FieldsUnnamed`是也是一个结构体字段，其两个字段:

- paren_token: token::Paren, 表明使用`Paren`(`()`)包裹
- unnamed: 具体内容字段，同样是一个`Punctuated`结构体

`Punctuated`有一个`iter`方法，产生一个迭代器，在这个迭代器中，有一个`Field`类型，这个类型是一个结构体。

#### Field类型

`Field`类型有下面几个字段，其中的几个字段和`Data`类型很像：

- attrs: Vec<Attribute> 属性集合
- vis: Visibility 可见性
- mutability: FieldMutability 可变性
- ident: Option<Ident> 字段
- ty: Type 类型标记
- colon_token: 标记，为`Token![:]`

`Type`类型是一个枚举，其值如下：

- Array(TypeArray) 数组类型
- BareFn(TypeBareFn) 函数指针
- Group(TypeGroup)
- ImplTrait(TypeImplTrait) impl 块类型
- Infer(TypeInfer) `_` 类型
- Macro(TypeMacro) 宏
- Never(TypeNever) Never
- Paren(TypeParen)
- Path(TypePath)
- Ptr(TypePrt) 原始指针
- Reference(TypeReference) 使用类型
- Slice(TypeSlice) 切片
- TraitObject(TypeTraitObject) 动态对像
- Tuple(TypeTuple) 元组
- Verbatim(TokenStream) TokenStream

`TypePath`中的`path`字段是一个结构体，其中有一个`segments`字段，这个字段也是一个`Punctuated`结构体。其中的`PageSegment`有`ident`和`arguments`
