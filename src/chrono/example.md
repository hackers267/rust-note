# 使用示例

## 格式化Rust标准库中的系统时间

```rust
use std::time::SystemTime;
use chrone::{TimeZone, Utc};


fn format_system(time: SystemTime) -> String {
  let unix_epoch = SystemTime::UNIX_EPOCH;
  let duration = time.duration_since(unix_epoch).unwrap().as_nanos();
  let transform_time = Utc.timestamp_nanos(duration.try_into().unwrap());
  transform_time.format("%Y-%m-%d %H-%M-%S").to_string()
}
```