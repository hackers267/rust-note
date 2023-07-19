+++
title = "time库的PrimitiveDateTime结构体"
date = 2023-07-19
[taxonomies]
tags = ["time"]
[extra]
toc = true
+++


# PrimitiveDateTime

`PrimitiveDateTime`是一个可以同时表示时间和时间的结构体。比如可以表示*2008 年 8 月 8 日 20 时 08 分 08 秒*这样的时间。

## 常量

`PrimitiveDateTime`中有几个常量：

- [MIN](#MIN)
- [MAX](#MAX)

### MIN

`MIN`常量用于表示`PrimitiveDateTime`的最小值，其值的具体大小取决于是否激活了`large-dates`标记。

- 没有激活`large-dates`时，其值为`-9999-01-01 00:00:00.0`
- 激活`large-dates`时，其值为`-999999-01-01 00:00:00.0`

### MAX

`MAX`常量用于表示`PrimitiveDateTime`的最大值，其值的具体大小也取决于是否激活了`large-dates`标记。

- 没有激活`large-dates`时，其值为`9999-12-31 23:59:59.999_999_999`
- 激活`large-dates`时，其值为`999999-12-31 23:59:59.999_999_999`

## 方法

### new

`new`方法用于创建一个新的`PrimitiveDateTime`的新实例。其签名如下：

```rust
fn new(date: Date, time: Time) -> Self
```

### date

`date`方法用于返回`PrimitiveDateTime`的 date 部分内容。其签名如下：

```rust
fn date(self) -> Date
```

从签名中，我们需要注意`date`方法会消费`PrimitiveDateTime`。

### time

`time`方法用于返回`PrimitiveDateTime`的 time 部分内容，其签名如下：

```rust
fn time(self) -> Time
```

从签名中，我们知道`time`方法会消费`PrimitiveDateTime`。

### year

`year`方法用于返回`PrimitiveDateTime`中的年份，其返回值是一个`i32`类型。签名如下：

```rust
fn year(self) -> i32
```

`year`方法同样到消费`PrimitiveDateTime`。

### month

`month`方法用于返回`PrimitiveDateTime`中的月份，其返回值是一个`Month`的枚举类型。签名如下：

```rust
fn month(self) -> Month
```

`month`方法同样会消费`PrimitiveDateTime`。其返回值是一个`Month`枚举，不是`u8`或`i8`类型。

### day

`day`方法用于获取`PrimitiveDateTime`中的天，其返回值是一个`u8`类型。签名如下：

```rust
fn day(self) -> u8
```

`day`方法同样会消费`PrimitiveDateTime`,`day`方法的返回值总是价于`1..=31`。

### ordinal

`ordinal`方法用于获取`PrimitiveDateTime`是一年中的第几天，返回值是一个`u16`类型。签名如下：

```rust
fn ordinal(self) -> u16
```

`ordinal`方法同样会消费`PrimitiveDateTime`，其返回值总是在`1..=366`范围内(在平年时，为`1..=365`的范围内);

### iso_week

`iso_week`用于获取`PrimitiveDateTime`中的第几个星期，其返回值是一个`u8`类型。签名如下：

```rust
fn iso_week(self) -> u8
```

`iso_week`方法同样会消费`PrimitiveDateTime`，其返回值总是在`1..=53`范围内。

### sunday_based_week

`sunday_based_week`用于获取在第一个星期日后的第几周，其返回值是一个`u8`类型。签名如下：

```rust
fn sunday_based_week(self) -> u8
```

`sunday_based_week`同样会消费`PrimitiveDateTime`，其起始日是第一个周日，返回值始终在`0..=53`的范围内。在`PrimitiveDateTime`中有一个以起始日为周一的方法：

`monday_based_week`

### monday_based_week

`monday_based_week`用于获取在第一个周一后的第几周，其返回值是一个`u8`类型。签名如下:

```rust
fn monday_based_week(self) -> u8
```

`monday_based_week`同样其消费`PrimitiveDateTime`，其起始日是第一个周一，返回值始终在`0..=53`的范围内。

> `sunday_based_week`和`monday_based_week`的不同之处在于其计算周次的起始日不同，`sunday_based_week`的起始日是第一个周日，而`monday_based_week`的起始日是第一个周一。

### to_calendar_date

`to_calendar_date`方法用于同时获取`PrimitiveDateTime`的年月日部分，其返回值是一个元组`(i32, Month, u8)`。签名如下：

```rust
fn to_calendar_date(self) -> (i32, Month, u8)
```

`to_calendar_date`方法同样会消费`PrimitiveDateTime`，返回一个同时表示年月日的元组`(i32, Month, u8)`。

在`PrimitiveDateTime`中已经有了`date`方法到获取`Date`类型的情况下，再提供了`to_calendar_date`方法，可以让我们更加方便的获取年月日。

### to_ordinal_date

`to_ordinal_date`方法用于同时获取`PrimitiveDateTime`中的年份和第几天，其返回值为一个元组`(i32, u16)`。签名如下：

```rust
fn to_ordinal_date(self) -> (i32, u16)
```

`to_ordinal_date`同样会消费`PrimitiveDateTime`，这个方法可以方便同时获取年份和第几天。

### to_iso_week_date

`to_iso_week_date`返回 ISO 中的年份和周次以及周几。返回得为一个元组`(i32, u8, Weekday)`;签名如下：

```rust
fn to_iso_week_date(self) -> (i32, u8, Weekday)
```

`to_iso_week_date`同样会消费`PrimitiveDateTime`，返回值中的第三个元素为一个`Weekday`枚举。可以方便同时获取到第几周和周几。

### weekday

`weekday`方法用于获取周几。返回值是一个`Weekday`的枚举。签名如下：

```rust
fn weekday(self) -> Weekday
```

`weekday`同样会消费`PrimitiveDateTime`。

### to_julian_day

`to_julian_day`用于获取日期的*儒略历*中的天数。其返回值为一个`i32`类型。签名如下：

```rust
fn to_julian_day(self) -> i32
```

`to_julian_day`同样会消费`PrimitiveDateTime`。

### as_hms

`as_hms`用于获取`PrimitiveDateTime`中的时分秒，返回值为一个三元元组`(u8, u8, u8)`。签名如下：

```rust
fn as_hms(self) -> (u8, u8, u8)
```

`as_hms`同样会消费`PrimitiveDateTime`。

### as_hms_milli

`as_hms_milli`用于获取`PrimitiveDateTime`中的时分秒和毫秒。返回值为一个四元元组`(u8, u8, u8, u16)`。签名如下：

```rust
fn as_hms_milli(self) -> (u8, u8, u8, u16)
```

`as_hms_milli`同样会消费`PrimitiveDateTime`。

### as_hms_micro

`as_hms_micro`用于获取`PrimitiveDateTime`中的时分秒和微秒。返回值为一个四元元组`(u8, u8, u8, u32)`。签名如下：

```rust
fn as_hms_micro() -> (u8, u8, u8, u32)
```

`as_hms_micro`方法同样会消费`PrimitiveDateTime`。

### as_hms_nano

`as_hms_nano`方法用于获取`PrimitiveDateTime`中的时分秒和纳秒。返回值为一个四元元组`(u8, u8, u8, u32)`。签名如下：

```rust
fn as_hms_nano(self) -> (u8, u8, u8, u32)
```

`as_hms_nano`方法同样会消费`PrimitiveDateTime`。

### hour

`hour`方法用于获取`PrimitiveDateTime`中的时。返回值是一个`u8`类型。签名如下：

```rust
fn hour(self) -> u8
```

`hour`方法同样会消费`PrimitiveDateTime`。

### minute

`minute`方法用于获取`PrimitiveDateTime`中的分钟数。返回值是一个`u8`类型。签名如下：

```rust
fn minute(self) -> u8
```

`minute`方法同样会消费`PrimitiveDateTime`。

### second

`second`方法用于获取`PrimitiveDateTime`中的秒数。返回值是一个`u8`类型。签名如下：

```rust
fn minute(self) -> u8
```

`second`方法同样会消费`PrimitiveDateTime`。

### millisecond

`millisecond`方法用于获取`PrimitiveDateTime`中的毫秒数。返回值是一个`u16`类型。签名如下：

```rust
fn millisecond(self) -> (u8, u8, u8, u16)
```

`millisecond`方法同样会消费`PrimitiveDateTime`。

### microsecond

`microsecond`方法用于获取`PrimitiveDateTime`中的微秒数。返回值是一个`u32`类型。签名如下:

```rust
fn microsecond(self) -> (u8, u8, u8, u32)
```

`microsecond`方法同样会消费`PrimitiveDateTime`。

### nanosecond

`nanosecond`方法用于获取`PrimitiveDateTime`中的纳秒数。返回值是一个`u32`类型。签名如下:

```rust
fn nanosecond(self) -> (u8, u8, u8, u32)
```

`nanosecond`方法同样会消费`PrimitiveDateTime`。

### assume_offset

`assume_offset`方法用于获取相对于[UtcOffset](./utcOffset.md)的偏移量，返回值是一个[OffsetDateTime](./offsetDateTime.md)。签名如下：

```rust
fn assume_offset(self, offset: UtcOffset) -> OffsetDateTime
```

`assume_offset`方法同样会消费`PrimitiveDateTime`。

### assume_utc

`assume_utc`方法用于获取`PrimitiveDateTime`在 UTC 中表示的时刻，返回值为`[OffsetDateTime](./offsetDateTime.md)`。签名如下:

```rust
fn assumt_utc(self) -> OffsetDateTime
```

`assume_utc`方法同样会消费`PrimitiveDateTime`。

### checked_add

`checked_add`方法用于计算在[duration](./duration.md)之后的日期时间，如果计算值溢出，那么返回`None`。签名如下：

```rust
fn checked_add(self,duration: Duration) -> Option<Self>
```

`checked_add`同样会消费`PrimitiveDateTime`。

### checked_sub

`checked_sub`方法用于计算在[duration](./duration.md)之前的日期时间，如果计算值溢出，那么返回`None`。签名如下:

```rust
fn checked_sub(self, duration: Duration) -> Option<Self>
```

`checked_sub`同样会消费`PrimitiveDateTime`。

### saturating_add

`saturating_add`方法用于计算在[duration](./duration.md)之后的日期时间，如果计算值溢出，则返回最大值。签名如下：

```rust
fn saturating_add(self, duration: Duration) -> Self
```

`saturating_add`方法同样会消费`PrimitiveDateTime`。

### saturating_sub

`saturating_sub`方法用于计算在[duration](./duration.md)之前的日期时间，如果计算值溢出，那么返回最小值。签名如下:

```rust
fn saturating_sub(self, duration: Duration) -> Self
```

`saturating_sub`方法同样会消费`PrimitiveDateTime`。

> 注意：下面的方法用于替换`PrimitiveDateTime`中的某个部分。

### replace_time

`replace_time`用于替换`PrimitiveDateTime`中的[Time](./time.md)部分。返回值为`Self`。签名如下：

```rust
fn replace_time(self, time: Time) -> Self
```

`replace_time`同样会消费`PrimitiveDateTime`。

### replace_date

`replace_date`用于替换`PrimitiveDateTime`中的[Date](./date.md)部分。返回值为`Self`。签名如下:

```rust
fn replace_date(self, date: Date) -> Self
```

`replace_date`同样会消费`PrimitiveDateTime`。

### replace_year

`replace_year`用于替换`PrimitiveDateTime`中的`year`部分。返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_year(self, year: i32) -> Result<Self, ComponentRange>
```

`replace_year`同样会消费`PrimitiveDateTime`

### replace_month

`replace_month`用于替换`PrimitiveDateTime`中的`month`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_month(self, month: Month) -> Result<Self, ComponentRange>
```

`replace_month`同样会消费`PrimitiveDateTime`

### replace_day

`replace_day`用于替换`PrimitiveDateTime`中的`day`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_day(self, day: u8) -> Result<Self, ComponentRange>
```

`replace_day`同样会消费`PrimitiveDateTime`

### replace_hour

`replace_hour`用于替换`PrimitiveDateTime`中的`hour`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_hour(self, hour: u8) -> Result<Self, ComponentRange>
```

`replace_hour`同样会消费`PrimitiveDateTime`

### replace_minute

`replace_minute`用于替换`PrimitiveDateTime`中的`minute`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_minute(self, minute: u8) -> Result<Self, ComponentRange>
```

`replace_minute`同样会消费`PrimitiveDateTime`

### replace_second

`replace_second`用于替换`PrimitiveDateTime`中的`second`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_second(self, second: u8) -> Result<Self, ComponentRange>
```

`replace_second`同样会消费`PrimitiveDateTime`

### replace_millisecond

`replace_millisecond`用于替换`PrimitiveDateTime`中的`millisecond`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_millisecond(self, millisecond: u16) -> Result<Self, ComponentRange>
```

`replace_millisecond`同样会消费`PrimitiveDateTime`

### replace_microsecond

`replace_microsecond`用于替换`PrimitiveDateTime`中的`microsecond`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_microsecond(self, microsecond: u32) -> Result<Self, ComponentRange>
```

`replace_microsecond`同样会消费`PrimitiveDateTime`

### replace_nanosecond

`replace_nanosecond`用于替换`PrimitiveDateTime`中的`nanosecond`部分，返回值为`Result<Self, ComponentRange>`。签名如下：

```rust
fn replace_nanosecond(self, nanosecond: u32) -> Result<Self, ComponentRange>
```

`replace_nanosecond`同样会消费`PrimitiveDateTime`。

> 下面的方法只有在开启`formatting`特性的时候才可以使用。

### format_into

使用提供的 format description 来格式化`PrimitiveDateTime`。返回值为`Result<usize, Format>`。

```rust
fn format_into(self, output: &mut impl Write, format: &impl Formattable + ?Sized)
```

> 使用`format_into`需要开启`formatting`特性。

### format

`format`方法按 format description 提供的格式把`PrimitiveDateTime`格式化为字符串。返回值为`Result<String, Format>`。签名如下：

```rust
fn format(self, format: &impl Formattable + ?Sized) -> Result<String, Format>
```

> 使用`format`需要使用`formatting`特性

> 下面的方法需要开启`parsing`特性

### parse

`parse`方法用于把字符串片段解析成`PrimitiveDateTime`。其签名如下：

```rust
fn parse(input: &str, description: &impl Parsable + ?Sized) -> Result<Self, Parse>
```

除了上面提到的方法外，`PrimitiveDateTime`还实现了以下的`trait`：

- Add
- AddAssign
- Sub
- SubAssign
- Arbitrary # 需要开启`quickcheck`特性
- Clone
- Debug
- Deserialize # 需要开启`serde`特性
- Serialize # 需要开启`serde`特性
- Distribution # 需要开启`rand`特性
- Hash
- Order
- PartialEq
- PartialOrd
- TryFrom
- Copy
- Eq
- StructuralEq
- StructuralPartialEq
