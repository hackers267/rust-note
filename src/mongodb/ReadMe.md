# 在Rust中使用Mongodb

## 添加依赖

    `Mongodb`为`rust`提供了官方的驱动。即[mongodb](https://docs.rs/mongodb/2.7.1/mongodb/);

    当我们需要在项目中使用`mongodb`时，可以把以下内容添加到`Cargo.toml`中：

```toml
[dependencies]
serde = "1.0.188"
futures = "0.3.28"
tokio = { version = "1.32.0", features = ["full" }

[dependencies.mongodb]
version = "2.7.1"
features = ["tokio-runtime"]
```

    当我们把以上内容添加到`Cargo.toml`中以后，我们就可以在项目中使用mongodb了。

    上面库的作用为：

- serde 用于序列化和反序列化rust结构体或枚举等和mongodb内部的m数据
- futures 用于扩展mongodb的一些查询，提供类似`StreamExt`的特型
- tokio 用于异步函数

## 连接数据库

     要使用`mongodb`就首先需要连接上我们要使用的数据库。如果你使用的是`mongodb`的`atlas`集群，那么可以在平台上直接获取链接。

    如果你使用的是自建的`mongodb`服务器，可以使用下面的这样的样式：`mongodb://localhost:27017`需要*localhost*和*27017*分别换成你的`mongodb`的服务器地址和接口，然后使用下面的代码:

```rust
use mongodb::{
    bson::{Document, doc},
    Client,
    Collection
};

#[tokio::main]
async fn main() -> mongodb::error::Result<()> {
    let uri = "<mongdob接连地址>";
    let client = Client::with_uri_str(uri).await?;
    let database = client.database("exampleDatabase");
    let collection: Collection<Document> = database.collection("example");
    let item = collection.find_one(doc! { "title": "hello" }, None).awiat?;
    println!("The item is \n {:?}", item);
    Ok(())
}
```

    我们使用上面的代码实现了数据库的连接和collection的查询。需要注意的是，我们需要把*uri*替换我们mongodb的真实的连接地址。
