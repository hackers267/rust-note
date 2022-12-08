# 函数

## 使用一个闭包作为参数的签名

当我们要创建一个高阶函数的时候，通常会使用接收一个闭包作为参数。这个时候，我们可以这样做：

```rust
fn diff_by<F>(list: Vec<u32>, predicate: F) -> Vec<u32>
    where F: Fn(&u32) -> bool {
    list
        .into_iter()
        .filter(predicate)
        .collect()
}
```
