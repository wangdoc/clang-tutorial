# wctype.h

wctype.h 提供 ctype.h 里面函数的宽字符版本。

## 宽字符类型判断函数

下面函数判断宽字符的类型。

- iswalnum()	测试宽字符是否为字母数字
- iswalpha()	测试宽字符是否为字母
- iswblank()	测试这是否是一个宽空白字符
- iswcntrl()	测试这是否是一个宽控制字符。
- iswdigit()	测试这个宽字符是否是数字
- iswgraph()	测试宽字符是否是可打印的非空格字符
- iswlower()	测试宽字符是否为小写
- iswprint()	测试宽字符是否可打印
- iswpunct()	测试宽字符是否为标点符号
- iswspace()	测试宽字符是否为空格
- iswupper()	测试宽字符是否为大写
- iswxdigit()	测试宽字符是否为十六进制数字

## wctype()，iswctype()

`iswctype()`是上一节各种宽字符类型判断函数的通用版本，必须与`wctype()`配合使用。

```c
int iswctype(wint_t wc, wctype_t desc);
```

`iswctype()`接受两个参数，第一个参数是一个需要判断类型的宽字符，第二个参数是宽字符类型描述，来自`wctype()`的返回值。

如果宽字符属于指定类型，`iswctype()`返回一个非零值，否则返回零。

`wctype()`用来获取某个种类宽字符的类型描述。

```c
wctype_t wctype(const char* property);
```

`wctype()`的参数是一个给定的字符串，可用的值如下：alnum、alpha、blank、cntrl、digit、graph、lower、print、punct、space、upper、xdigit。

`wctype()`的返回值的类型为 wctype_t，通常是一个整数。如果参数是一个无效值，则返回`0`。

```c
if (iswctype(c, wctype("digit")))
// 相当于
if (iswdigit(c))
```

上面示例用来判断宽字符`c`是否为数值，相当于`iswdigit()`。

`iswctype()`的完整类型判断如下。

```c
iswctype(c, wctype("alnum"))	// 相当于 iswalnum(c)
iswctype(c, wctype("alpha"))	// 相当于 iswalpha(c)
iswctype(c, wctype("blank"))	// 相当于 iswblank(c)
iswctype(c, wctype("cntrl"))	// 相当于 iswcntrl(c)
iswctype(c, wctype("digit"))	// 相当于 iswdigit(c)
iswctype(c, wctype("graph"))	// 相当于 iswgraph(c)
iswctype(c, wctype("lower"))	// 相当于 iswlower(c)
iswctype(c, wctype("print"))	// 相当于 iswprint(c)
iswctype(c, wctype("punct"))	// 相当于 iswpunct(c)
iswctype(c, wctype("space"))	// 相当于 iswspace(c)
iswctype(c, wctype("upper"))	// 相当于 iswupper(c)
iswctype(c, wctype("xdigit"))	// 相当于 iswxdigit(c)
```

## 大小写转换函数

wctype.h 提供以下宽字符大小写转换函数。

- towlower()	将大写宽字符转换为小写
- towupper()	将小写宽字符转换为大写
- towctrans()	宽字符大小写转换的通用函数
- wctrans()	大小写转换的辅助函数，配合 towctrans() 使用

先看`towlower()`和`towupper()`的用法示例。

```c
towlower(L'B') // b
towupper(L'e') // E
```

`towctrans()`和`wctrans()`的原型如下。

```c
wint_t towctrans(wint_t wc, wctrans_t desc);
wctrans_t wctrans(const char* property);
```

下面是它们的用法示例。

```c
towctrans(c, wctrans("toupper"))	// 相当于 towupper(c)
towctrans(c, wctrans("tolower"))	// 相当于 towlower(c)
```
