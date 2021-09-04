# ctype.h

`ctype.h`头文件定义了一系列字符处理函数的原型。

## 字符测试函数

这些函数用来判断字符是否属于某种类型。

- `isalnum()`：是否为字母数字
- `isalpha()`：是否为字母
- `isdigit()`：是否为数字
- `isxdigit()`：是否为十六进制数字符
- `islower()`：是否为小写字母
- `isupper()`：是否为大写字母
- `isblank()`：是否为标准的空白字符（包含空格、水平制表符或换行符）
- `isspace()`：是否为空白字符（空格、换行符、换页符、回车符、垂直制表符、水平制表符等）
- `iscntrl()`：是否为控制字符，比如 Ctrl + B
- `isprint()`：是否为可打印字符
- `isgraph()`：是否为空格以外的任意可打印字符
- `ispunct()`：是否为标点符号（除了空格、字母、数字以外的可打印字符）

它们接受一个待测试的字符作为参数。注意，参数类型为`int`，而不是`char`，因为它们允许 EOF 作为参数。

如果参数字符属于指定类型，就返回一个非零整数（通常是`1`，表示为真），否则返回`0`（表示为伪）。

下面是一个例子，用户输入一个字符，程序判断是否为英文字母。

```c
#include <stdio.h>
#include <ctype.h>

int main(void) {
  char ch = getchar();

  if (isalpha(ch))
    printf("it is an alpha character.\n");
  else
    printf("it is not an alpha character.\n");

  return 0;
}
```

## 字符映射函数

这一类函数返回字符的某种对应形式，主要有两个函数。

- `tolower()`：如果参数是大写字符，返回小写字符，否则返回原始参数。
- `toupper()`：如果参数是小写字符，返回大写字符，否则返回原始参数。

```c
// 将字符转为大写
ch = toupper(ch);
```

注意，这两个函数不会改变原始字符。
