# 使用cargo build构建

`cargo build`用于对`rust`代码的构建操作，一般用于构建命令行工具或是应用。

## 优化包的大小

当我们使用cargo打包应用的时候，很多时候包的大小都不太理想，比较大。这个时候，我们可以通过下面的方式来优化一下：

1. 使用`carge build --release`打包。
2. 设置`profile.release`选项
```toml
[profile.release]
opt-level = 'z'
strip = true
lto = true
```

在使用`profile.release`时，`opt-level`是优化等级修改，如果不是很必要，不就修改。而`lto`是通过增加链接时间来换空间的一种做法。