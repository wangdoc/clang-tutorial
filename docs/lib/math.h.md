# math.h

`math.h`头文件提供了很多数学函数。

很多数学函数的返回值是 double 类型，但是同时提供 float 类型与 long double 类型的版本，比如`pow()`函数就还有`powf()`和`powl()`版本。

```c
double      pow(double x, double y); 
float       powf(float x, float y);
long double powl(long double x, long double y);
```

为了简洁，下面就略去了函数的`f`后缀（float 类型）和`l`后缀（long double）版本。

## 类型和宏

math.h 新定义了两个类型别名。

- float_t：（当前系统）最有效执行 float 运算的类型，宽度至少与 float 一样。
- double_t：（当前系统）最有效执行 double 运算的类型，宽度至少与 double 一样。

它们的具体类型可以通过宏`FLT_EVAL_METHOD`来了解。

| FLT_EVAL_METHOD 的值 |	float_t 对应的类型 | double_t 对应的类型 |
|-----------------|--------------|--------------|
| 0	| float	| double |
| 1	| double | double |
| 2	| long double	| long double |
| 其他 | 由实现决定 |	由实现决定 |

math.h 还定义了一些宏。

- `INFINITY`：表示正无穷，返回一个 float 类型的值。
- `NAN`：表示非数字（Not-A-Number），返回一个 float 类型的值。

## 错误类型

数学函数的报错有以下类型。

- Range errors：运算结果不能用函数返回类型表示。
- Domain errors：函数参数不适用当前函数。
- Pole errors：参数导致函数的极限值变成无限。
- Overflow errors：运算结果太大，导致溢出。
- Underflow errors：运算结果太小，导致溢出。

变量`math_errhandling`提示了当前系统如何处理数学运算错误。

| math_errhandling 的值 | 描述 |
|-----------------------|-----|
| MATH_ERRNO | 系统使用 errno 表示数学错误 |
| MATH_ERREXCEPT | 系统使用异常表示数学错误 |
| MATH_ERREXCEPT	| 系统同时使用两者表示数学错误 |

## 数值类型

数学函数的参数可以分成以下几类：正常值，无限值，有限值和非数字。

下面的函数用来判断一个值的类型。

- fpclassify()：返回给定浮点数的分类。
- isfinite()：如果参数不是无限或 NaN，则为真。
- isinf()：如果参数是无限的，则为真。
- isnan()：如果参数不是数字，则为真。
- isnormal()：如果参数是正常数字，则为真。

下面是一个例子。

```c
isfinite(1.23)    // 1
isinf(1/tan(0))   // 1
isnan(sqrt(-1))   // 1
isnormal(1e-310)) // 0
```

## signbit()

`signbit()`判断参数是否带有符号。如果参数为负值，则返回1，否则返回0。

```c
signbit(3490.0) // 0
signbit(-37.0)  // 1
```

## 三角函数

以下是三角函数，参数为弧度值。

- acos()：反余弦。
- asin()：反正弦。
- atan()：反正切
- atan2()：反正切。
- cos()：余弦。
- sin()：正弦。
- tan()：正切。

不要忘了，上面所有函数都有 float 版本（函数名加上 f 后缀）和 long double 版本（函数名加上 l 后缀）。

下面是一个例子。

```c
cos(PI/4) // 0.707107
```

## 双曲函数

以下是双曲函数，参数都为浮点数。

- acosh()：反双曲余弦。 
- asinh()：反双曲正弦。
- atanh()：反双曲正切。
- cosh()：双曲余弦。
- tanh()：双曲正切。
- sinh()：双曲正弦。 

## 指数函数和对数函数

以下是指数函数和对数函数，它们的返回值都是 double 类型。

- exp()：计算欧拉数 e 的乘方，即 e<sup>x</sup>。
- exp2()：计算 2 的乘方，即 2<sup>x</sup>。
- expm1()：计算 e<sup>x</sup> - 1。
- log()：计算自然对数，`exp()`的逆运算。
- log2()：计算以2为底的对数。
- log10()：计算以10为底的对数。
- logp1()：计算一个数加 1 的自然对数，即`ln(x + 1)`。
- logb()：计算以宏`FLT_RADIX`（一般为2）为底的对数，但只返回整数部分。

下面是一些例子。

```c
exp(3.0) // 20.085500
log(20.0855) // 3.000000
log10(10000) // 3.000000
```

如果结果值超出了 C 语言可以表示的最大值，函数将返回`HUGE_VAL`，它是一个在`math.h`中定义的 double 类型的值。

如果结果值太小，无法用 double 值表示，函数将返回0。以上这两种情况都属于出错。

## frexp()

`frexp()`将参数分解成浮点数和指数部分（2为底数），比如 1234.56 可以写成 0.6028125 * 2<sup>11</sup>，这个函数就能分解出 0.6028125 和 11。

```c
double frexp(double value, int* exp);
```

它接受两个参数，第一个参数是用来分解的浮点数，第二个参数是一个整数变量指针。

它返回小数部分，并将指数部分放入变量`exp`。如果参数为`0`，则返回的小数部分和指数部分都为`0`。

下面是一个例子。

```c
double frac;
int expt;

// expt 的值是 11
frac = frexp(1234.56, &expt);

// 输出 1234.56 = 0.6028125 x 2^11
printf("1234.56 = %.7f x 2^%d\n", frac, expt);
```

## ilogb()

`ilogb()`返回一个浮点数的指数部分，指数的基数是宏`FLT_RADIX`（一般是`2`）。

```c
int ilogb(double x);
```

它的参数为`x`，返回值是 log<sub>r</sub>|x|，其中`r`为宏`FLT_RADIX`。

下面是用法示例。

```c
ilogb(257) // 8
ilogb(256) // 8
ilogb(255) // 7
```

## ldexp()

`ldexp()`将一个数乘以2的乘方。它可以看成是`frexp()`的逆运算，将小数部分和指数部分合成一个`f * 2^n`形式的浮点数。

```c
double ldexp(double x, int exp);
```

它接受两个参数，第一个参数是乘数`x`，第二个参数是2的指数部分`exp`，返回“x * 2<sup>exp</sup>”。

```c
ldexp(1, 10) // 1024.000000
ldexp(3, 2) // 12.000000
ldexp(0.75, 4) // 12.000000
ldexp(0.5, -1) // 0.250000
```

## modf() 

`modf()`函数提取一个数的整数部分和小数部分。

```c
 double modf(double value, double* iptr);
 ```

它接受两个参数，第一个参数`value`表示待分解的数值，第二个参数是浮点数变量`iptr`。返回值是`value`的小数部分，整数部分放入变量`double`。

下面是一个例子。

```c
// int_part 的值是 3.0
modf(3.14159, &int_part); // 返回 0.14159
```

## scalbn() 

`scalbn()`用来计算“x * r<sup>n</sup>”，其中`r`是宏`FLT_RADIX`。

```c
double scalbn(double x, int n);
```

它接受两个参数，第一个参数`x`是乘数部分，第二个参数`n`是指数部分，返回值是“x * r<sup>n</sup>”。

下面是一些例子。

```c
scalbn(2, 8) // 512.000000
```

这个函数有多个版本。

- scalbn()：指数 n 是 int 类型。
- scalbnf()：float 版本的 scalbn()。
- scalbnl()：long double 版本的 scalbn()。
- scalbln()：指数 n 是 long int 类型。
- scalblnf()：float 版本的 scalbln()。
- scalblnl()：long double 版本的 scalbln()。

## round()

`round()`函数以传统方式进行四舍五入，比如`1.5`舍入到`2`，`-1.5`舍入到`-2`。

```c
double round(double x);
```

它返回一个浮点数。

下面是一些例子。

```c
round(3.14)  // 3.000000
round(3.5)   // 4.000000
round(-1.5)  // -2.000000
round(-1.14) // -1.000000
```

它还有一些其他版本。

- lround()：返回值是 long int 类型。
- llround()：返回值是 long long int 类型。

## trunc() 

`trunc()`用来截去一个浮点数的小数部分，将剩下的整数部分以浮点数的形式返回。

```c
double trunc(double x);
```

下面是一些例子。

```c
trunc(3.14)  // 3.000000
trunc(3.8)   // 3.000000
trunc(-1.5)  // -1.000000
trunc(-1.14) // -1.000000
```

## ceil()

`ceil()`返回不小于其参数的最小整数（double 类型），属于“向上舍入”。

```c
double ceil(double x);
```

下面是一些例子。

```c
ceil(7.1) // 8.0
ceil(7.9) // 8.0
ceil(-7.1) // -7.0
ceil(-7.9) // -7.0
```

## floor()

`floor()`返回不大于其参数的最大整数，属于“向下舍入”。

```c
double floor(double x);
```

下面是一些例子。

```c
floor(7.1) // 7.0
floor(7.9) // 7.0
floor(-7.1) // -8.0
floor(-7.9) // -8.0
```

下面的函数可以实现“四舍五入”。

```c
double round_nearest(double x) {
  return x < 0.0 ? ceil(x - 0.5) : floor(x + 0.5);
}
```

## fmod()

`fmod()`返回第一个参数除以第二个参数的余数，就是余值运算符`%`的浮点数版本，因为`%`只能用于整数运算。

```c
double fmod(double x, double y);
```

它在幕后执行的计算是`x - trunc(x / y) * y`，返回值的符号与`x`的符号相同。

```c
fmod(5.5, 2.2)  //  1.100000
fmod(-9.2, 5.1) // -4.100000
fmod(9.2, 5.1)  //  4.100000
```

## 浮点数比较函数

以下函数用于两个浮点数的比较，返回值的类型是整数。

- isgreater()：返回`x > y`的结果。
- isgreaterequal()：返回`x >= y`的结果。
- isless()：返回`x < y`的结果。
- islessequal()：返回`x <= y`的结果。
- islessgreater()：返回`(x < y) || (x > y)`的结果。

下面是一些例子。

```c
isgreater(10.0, 3.0)   // 1
isgreaterequal(10.0, 10.0)   // 1
isless(10.0, 3.0)  // 0
islessequal(10.0, 3.0)   // 0
islessgreater(10.0, 3.0)   // 1
islessgreater(10.0, 30.0)   // 1
islessgreater(10.0, 10.0)   // 0
```

## isunordered()

`isunordered()`返回两个参数之中，是否存在 NAN。

```c
int isunordered(any_floating_type x, any_floating_type y);
```

下面是一些例子。

```c
isunordered(1.0, 2.0)    // 0
isunordered(1.0, sqrt(-1))  // 1
isunordered(NAN, 30.0)  // 1
isunordered(NAN, NAN)   // 1
```

## 其他函数

下面是 math.h 包含的其它函数。

- pow()：计算参数`x`的`y`次方。
- sqrt()：计算一个数的平方根。
- cbrt()：计算立方根。
- fabs()：计算绝对值。
- hypot()：根据直角三角形的两条直角边，计算斜边。
- fmax()：返回两个参数之中的最大值。
- fmin()：返回两个参数之中的最小值。
- remainder()：返回 IEC 60559 标准的余数，类似于`fmod()`，但是余数范围是从`-y/2`到`y/2`，而不是从`0`到`y`。
- remquo()：同时返回余数和商，余数的计算方法与`remainder()`相同。
- copysign()：返回一个大小等于第一个参数、符号等于第二个参数的值。
- nan()：返回 NAN。  
- nextafter()：获取下一个（或者上一个，具体方向取决于第二个参数`y`）当前系统可以表示的浮点值。
- nextoward()：与`nextafter()`相同，除了第二个参数是 long double 类型。
- fdim()：如果第一个参数减去第二个参数大于`0`，则返回差值，否则返回`0`。
- fma()：以快速计算的方式，返回`x * y + z`的结果。
- nearbyint()：在当前舍入方向上，舍入到最接近的整数。当前舍入方向可以使用`fesetround()`函数设定。
- rint()：在当前舍入方向上，舍入到最接近的整数，与`nearbyint()`相同。不同之处是，它会触发浮点数的`INEXACT`异常。
- lrint()：在当前舍入方向上，舍入到最接近的整数，与`rint()`相同。不同之处是，返回值是一个整数，而不是浮点数。
- erf()：计算一个值的误差函数。
- erfc()：计算一个值的互补误差函数。
- tgamma()：计算 Gamma 函数。
- lgamma()：计算 Gamma 函数绝对值的自然对数。

下面是一些例子。

```c
pow(3, 4) // 81.000000
sqrt(3.0) // 1.73205
cbrt(1729.03) // 12.002384
fabs(-3490.0) // 3490.000000
hypot(3, 4) // 5.000000
fmax(3.0, 10.0) // 10.000000
fmin(10.0, 3.0) //  3.000000
```
