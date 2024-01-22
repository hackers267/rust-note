# 构建

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
codegen-units = 1
panic = 'abort'
```

在使用`profile.release`时，`opt-level`是优化等级修改，如果不是很必要，不就修改。而`lto`是通过增加链接时间来换空间的一种做法。

Cargo默认会启用16个并行代码生成单元，对编译速度在提升，但会妨碍某些优化的进行，可以通过 `codegen-units = 1` 限制生成单元数，来提升某些优化。  


> 前面的优化对程序的行为都不会产生任何影响，然而这个优化会。

众所周知，Rust 程序在 panic 时会生成栈回溯，方便定位问题。而这个 flag 会禁用这种行为——请自行权衡使用。

最小化依赖

1. 去除不必要的依赖
2. 禁用不必要的 feature

使用 `xargo` 进行 `libstd` 优化

在 `Xargo.toml` 添加以下代码：

```toml
[dependencies]
std = { default-features = false, features=[ "panic_immediate_abort" ] }[dependencies]
```

```bash
xargo build --target x86_64-unknown-linux-gnu --release 
```

使用 *UPX* 压缩 

```bash
upx -9 target/x86_64-unknown-linux-gnu/release/ren3  
```
