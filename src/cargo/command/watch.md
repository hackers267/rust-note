# cargo-watch

`cargo-watch`是一个用于观测的你的项目，当项目内容发生变化的时候，自动运行运行的一个`cargo`组件。

## 安装

`cargo-watch`的安装是很简单的，可以直接使用`cargo install cargo-watch`安装即可。

## 常用命令

在使用*cargo watch*中，最简单的命令就是直接运行*cargo watch*，在这个时候，*cargo watch*会在后台自动运行`cargo check`命令。

常用参数如下：

- *-x, --exec <cmd>* 指定在后台运行的命令，默认情况下是*check*命令，例如可以在后台运行*run* : `cargo watch -x run`
- *--features <features>* 指定调用cargo时传递的*features*列表
- *-w, --watch <watch>* 指定要监测的文件或目录
- *-d, --delay <delay>* 指定在文件改变后，延迟多少秒运行后台指定的命令，默认是*0.5*秒
- *-s, --shell <cmd>* 指定在文件改变后，要运行的*shell*命令，而不是*cargo*命令
- *--env-file <env-files>* 从指定的文件中构建环境变量
- *-E, --env <env-vars>* 从
