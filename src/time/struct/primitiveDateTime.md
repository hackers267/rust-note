# PrimitiveDateTime

`PrimitiveDateTime`是一个可以同时表示时间和时间的结构体。比如可以表示*2008年8月8日20时08分08秒*这样的时间。

## 常量

`PrimitiveDateTime`中有几个常量：

- [MIN](#min)
- [MAX](#max)

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

`date`方法用于返回`PrimitiveDateTime`的date部分内容。其签名如下：

```rust
fn date(self) -> Date
```

从签名中，我们需要注意`date`方法会消费`PrimitiveDateTime`。

### time

`time`方法用于返回`PrimitiveDateTime`的time部分内容，其签名如下：
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

###  sunday_based_week

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

### to_orinal_date


// TODO: 更多方法