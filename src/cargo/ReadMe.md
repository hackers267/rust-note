# Cargo

Cargo是rust中的包管理工具，其定位类似于node中的npm，python中的pip。都是为了方便我们在项目工程化的时候，为我们提供更好的依赖管理。其主要的配置文件是`Cargo.toml`文件以及其对应的`Cargo.lock`文件。

## 命令行

`Cargo`中比较常用的就是使用命令行对Rust应用进行启动，代码检查和进行测试，打包等工作，当然，也可以使用Cargo命令对其依赖进行更新管理。

### 使用Cargo命令对依赖进行更新

当我们想要把rust中的依赖更新到当前依赖的最新兼容版本的时候，我们可以使用`cargo update`命令。

`cargo update`可以对当前项目中的所有依赖进行兼容性版本更新。在我们要对指定依赖进行更新的时候，我们可以使用下面的命令：
```shell
cargo update -p hutools
```
