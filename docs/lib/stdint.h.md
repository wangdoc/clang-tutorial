# stdint.h

## 固定宽度的整数类型

stdint.h 定义了一些固定宽度的整数类型别名，主要有下面三类。

- 宽度完全确定的整数`intN_t`，比如`int32_t`。
- 宽度不小于某个大小的整数`int_leastN_t`，比如`int_least8_t`。
- 宽度不小于某个大小、并且处理速度尽可能快的整数`int_fastN_t`，比如`int_fast64_t`。

上面所有类型都是有符号的，类型名前面可以加一个前缀`u`，表示无符号类型，比如`uint16_t`。

C 语言标准要求定义以下类型。

- int8_t（可选）      uint8_t（可选）
- int16_t（可选）     uint16_t（可选）
- int32_t（可选）    uint32_t（可选）
- int64_t（可选）     uint64_t（可选）
- int_least8_t      uint_least8_t
- int_least16_t     uint_least16_t
- int_least32_t     uint_least32_t
- int_least64_t     uint_least64_t
- int_fast8_t       uint_fast8_t
- int_fast16_t      uint_fast16_t
- int_fast32_t      uint_fast32_t
- int_fast64_t      uint_fast64_t

## 最大宽度的整数类型

以下两个类型表示当前系统可用的最大宽度整数。

- intmax_t
- uintmax_t

如果想要尽可能大的整数时，可以使用上面类型。

## 固定宽度的整数常量

以下一些带参数的宏，可以生成固定宽度的整数常量。

- INT8_C(x)     UINT8_C(x)
- INT16_C(x)    UINT16_C(x)
- INT32_C(x)    UINT32_C(x)
- INT64_C(x)    UINT64_C(x)
- INTMAX_C(x)   UINTMAX_C(x)

下面是用法示例。

```c
uint16_t x = UINT16_C(12);
intmax_t y = INTMAX_C(3490);
```

## 固定宽度的整数极限值

下面一些宏代表了固定宽度的整数最大值和最小值。

- INT8_MAX           INT8_MIN           UINT8_MAX
- INT16_MAX          INT16_MIN          UINT16_MAX
- INT32_MAX          INT32_MIN          UINT32_MAX
- INT64_MAX          INT64_MIN          UINT64_MAX
- INT_LEAST8_MAX     INT_LEAST8_MIN     UINT_LEAST8_MAX
- INT_LEAST16_MAX    INT_LEAST16_MIN    UINT_LEAST16_MAX
- INT_LEAST32_MAX    INT_LEAST32_MIN    UINT_LEAST32_MAX
- INT_LEAST64_MAX    INT_LEAST64_MIN    UINT_LEAST64_MAX
- INT_FAST8_MAX      INT_FAST8_MIN      UINT_FAST8_MAX
- INT_FAST16_MAX     INT_FAST16_MIN     UINT_FAST16_MAX
- INT_FAST32_MAX     INT_FAST32_MIN     UINT_FAST32_MAX
- INT_FAST64_MAX     INT_FAST64_MIN     UINT_FAST64_MAX
- INTMAX_MAX         INTMAX_MIN         UINTMAX_MAX

注意，所有无符号整数类型的最小值都为0，所以没有对应的宏。

## 占位符

C 语言还在头文件 inttypes.h 里面，为上面类型定义了`printf()`和`scanf()`的占位符，参见《inttypes.h》一章。
