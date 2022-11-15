# 基础问题

### 获取一个元素在数组中的索引

```rust
fn index(arr: &[&str], str: &str) -> Option<usize> {
    arr.iter()
        .position(|&x| x == str)
}
```