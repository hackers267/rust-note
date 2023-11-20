# itertools中的函数

## all

`all`用于判断迭代器中的内容是否全部符合给定的断言。其和标准库的的`all`具有相同的功能。其使用示例如下:

```rust
use itertools::all;

assert!(all(&[1,2,3], |elt| *elt > 0));
```

## any

`any`用于判断迭代器中是否至少用一项满足给定的断言。如果有，则返回`true`。其和标准库中的`any`具有相同的功能。
使用示例如下:

```rust
use itertools::any;

assert!(any(&[0,-1,2], |elt| *elt > 0));
```

## chain

`chain`用于依次连接两个迭代器，并生成一个新的迭代器。使用示例如下：

```rust
use itertools::chain;

let mut result:Vec<i32> = Vec::new();

for element in chain(&[1,2,3], &[4]) {
  result.push(element);
}

assert_eq(result, vec![1,2,3,4]);
```

## cloned

`clonde`用于创建一个新的迭代器，其中的值来自于原迭代器的复制。等效于`Iterator::cloned`。

## concat

`concat`用于把所有的迭代器元素全部归一到一个迭代器中。等效于`Iterator::concat`。

示例如下：

```rust
use itertools::concat;

let input = vec![vec![1], vec![2, 3], vec![4, 5, 6]];

assert_eq(concat(input), vec![1, 2, 3, 4, 5, 6]);
```

需要注意的是，`concat`方法会其中的内容。

## cons_tuples

把形如`((A, B), C)` 形式的迭代器转换为`(A, B, C)` 形式。

## enumerate

为迭代器添加索引，其等效于`Iterator::enumerate`。

## equal

如果两个迭代器生成的元素完全相等。那么函数返回`true`，否则返回`false`。等同于`iterator::eq`。

示例如下:

```rust
assert!(itertools::equal(vec![1, 2, 3], 1..4));
assert!(!itertools::equal(vec![0, 0, 0], vec![0, 0]));
```

## fold

在*iteralbe*的应用归一操作，相当于`iterator::fold`。

示例如下：

```rust
use itertools::fold;

assert_eq!(fold(&[1, 2, 3], 0., |a, &b| f32::max(a, b), 3.));
```

## interleave

创建一个迭代器，交替排列两个迭代器中的元素。示例如下:

```rust
use itertools::interleave;

let result = interleave(&[1, 2, 3], &[4, 5, 6]);
assert_eq!(result, [1, 4, 2, 5, 3, 6]);
```
