# 命令行应用

使用rust创建命令行应用，需要使用到一些内置的API，为了方便，常常还需要使用`clap`依赖库。这样就可以方便的开发一些命令行应用了。需要使用到的内置API主要是`process`的`Command`。下面先看一个示例：

```
fn ls() {
    let output = Command::new("ls").arg("-alh").output().unwrap();
    let output = String::from_utf8(output.stdout).unwrap();
    println!("{}", output);
    }
```