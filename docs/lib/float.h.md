# float.h

`float.h`定义了浮点数类型 float、double、long double 的一些宏，规定了这些类型的范围和精度。

(1) `FLT_ROUNDS`

宏`FLT_ROUNDS`表示当前浮点数加法的四舍五入方向。

它有以下可能的值。

- -1：不确定。
- 0：向零舍入。
- 1：向最近的整数舍入。
- 2：向正无穷方向舍入。
- 3：向负无穷方向舍入。

（2）`FLT_RADIX`

宏`FLT_RADIX`表示科学计数法的指数部分的底（base），一般总是2。

（3）浮点数类型的最大值

- `FLT_MAX`
- `DBL_MAX`
- `LDBL_MAX`

（4）浮点数类型的最小正值

- `FLT_MIN`
- `DBL_MIN`
- `LDBL_MIN`

（5）两个同类型浮点数之间可表示的最小差值（最小精度）

- `FLT_EPSILON`
- `DBL_EPSILON`
- `LDBL_EPSILON`

（6）`DECIMAL_DIG`

宏`DECIMAL_DIG`表示十进制有效位数。

（7）`FLT_EVAL_METHOD`

宏`FLT_EVAL_METHOD`表示浮点数运算时的类型转换。

它可能有以下值。

- -1：不确定。
- 0：在当前类型中运算。
- 1：float 和 double 类型的运算使用 double 类型的范围和精度求值。
- 2：所有浮点数类型的运算使用 long double 类型的范围和精度求值。

（8）浮点数尾数部分的个数

- `FLT_MANT_DIG`
- `DBL_MANT_DIG`
- `LDBL_MANT_DIG`

（9）浮点数指数部分有效数字的个数（十进制）

- `FLT_DIG`
- `DBL_DIG`
- `LDBL_DIG`

（10）科学计数法的指数部分的最小次幂（负数）

- `FLT_MIN_EXP`
- `DBL_MIN_EXP`
- `LDBL_MIN_EXP`

（11）科学计数法的指数部分的十进制最小次幂（负数）

- `FLT_MIN_10_EXP`
- `DBL_MIN_10_EXP`
- `LDBL_MIN_10_EXP`

（12）科学计数法的指数部分的最大次幂

- `FLT_MAX_EXP`
- `DBL_MAX_EXP`
- `LDBL_MAX_EXP`

（13）科学计数法的指数部分的十进制最大次幂

- `FLT_MAX_10_EXP`
- `DBL_MAX_10_EXP`
- `LDBL_MAX_10_EXP`
