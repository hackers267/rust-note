+++
title = "vec"
date = 2023-07-19
[taxonomies]
tags = ["std", "collections"]
[extra]
toc = true
+++
# Vec

`Vec`是rust中的可变长数组，和大部分语言一样，在`vec`中，只能存储同一种类型。

## `len`和`capacity`的不同

作为一种可变长数组的实现，创建一个新的`vec`的时候，系统会自动给*vec*分配一个占用内存，这个占用的内存的大小就是容量，即`capacity`。当在*vec*里添加内容的时候，会使用已经分配给`vec`的内存，这个时候，元素实际占用的内容就是`len`。这就是`capacity`和`len`的不同。