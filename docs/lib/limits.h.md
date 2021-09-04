# limits.h

`limits.h`提供了用来定义各种整数类型（包括字符类型）取值范围的宏。

- `CHAR_BIT`：每个字符包含的二进制位数。
- `SCHAR_MIN`：signed char 类型的最小值。
- `SCHAR_MAX`：signed char 类型的最大值。
- `UCHAR_MAX`：unsiged char 类型的最大值。
- `CHAR_MIN`：char 类型的最小值。
- `CHAR_MAX`：char 类型的最大值。
- `MB_LEN_MAX`：多字节字符最多包含的字节数。
- `SHRT_MIN`：short int 类型的最小值。
- `SHRT_MAX`：short int 类型的最大值。
- `USHRT_MAX`：unsigned short int 类型的最大值。
- `INT_MIN`：int 类型的最小值。
- `INT_MAX`：int 类型的最大值。
- `UINT_MAX`：unsigned int 类型的最大值。
- `LONG_MIN`：long int 类型的最小值。
- `LONG_MAX`：long int 类型的最大值。
- `ULONG_MAX`：unsigned long int 类型的最大值。
- `LLONG_MIN`：long long int 类型的最小值。
- `LLONG_MAX`：long long int 类型的最大值。
- `ULLONG_MAX`：unsigned long long int 类型的最大值。

下面的示例是使用预处理指令判断，int 类型是否可以用来存储大于 100000 的数。

```c
#if INT_MAX < 100000
  #error int type is too small
#endif
```

上面示例中，如果 int 类型太小，预处理器会显示一条出错消息。

可以使用`limit.h`里面的宏，为类型别名选择正确的底层类型。

```c
#if INT_MAX >= 100000
  typedef int Quantity;
#else
  typedef long int Quantity;
#endif
```

上面示例中，如果整数类型的最大值（`INT_MAX`）不小于100000，那么类型别名`Quantity`指向`int`，否则就指向`long int`。
