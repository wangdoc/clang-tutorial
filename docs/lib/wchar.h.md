# wchar.h

宽字符使用两个或四个字节表示一个字符，导致 C 语言常规的字符处理函数都会失效。wchar.h 定义了许多宽字符专用的处理函数。

## 类型别名和宏

wchar.h 定义了一个类型别名 wint_t，表示宽字符对应整数值。

wchar.h 还定义了一个宏 WEOF，表示文件结束字符 EOF 的宽字符版。

## btowc()，wctob()

`btowc()`将单字节字符转换为宽字符，`wctob()`将宽字符转换为单字节字符。

```c
wint_t btowc(int c);
int wctob(wint_t c);
```

`btowc()`返回一个宽字符。如果参数是 EOF，或转换失败，则返回 WEOF。

`wctob()`返回一个单字节字符。如果参数是 WEOF，或者参数宽字符无法对应单个的单字节字符，则返回 EOF。

下面是用法示例。

```c
wint_t wc = btowc('B'); 

// 输出宽字符 B
wprintf(L"Wide character: %lc\n", wc);

unsigned char c = wctob(wc);

// 输出单字节字符 B
wprintf(L"Single-byte character: %c\n", c);
```

## fwide()

`fwide()`用来设置一个字节流是宽字符流，还是多字节字符流。

如果使用宽字符专用函数处理字节流，就会默认设置字节流为宽字符流，否则就需要使用`fwide()`显式设置。

```c
int fwide(FILE* stream, int mode);
```

它接受两个参数，第一个参数是文件指针，第二个参数是字节流模式，有三种选择。

- 0：字节流模式保持原样。
- -1（或其他负值）：设为多字节字符流。
- 1（或其他正值）：设为宽字符流。

`fwide()`的返回值也分成三种情况：如果是宽字符流，返回一个正值；如果是多字节字符流，返回一个负值；如果是普通字符流，返回`0`。

一旦设置了字节流模式，就无法再更改。

```c
#include <stdio.h>
#include <wchar.h>

int main(void) {
  wprintf(L"Hello world!\n");
  int mode = fwide(stdout, 0);
  wprintf(L"Stream is %ls-oriented\n", mode < 0 ? L"byte" : L"wide");
}
```

上面示例中，`wprintf()`将字节流隐式设为宽字符模式，所以`fwide(stdout, 0)`的返回值大于零。

## 宽字符专用函数

下面这些函数基本都是 stdio.h 里面的字符处理函数的宽字符版本，必须使用这些函数来操作宽字符。

- fgetwc()	从宽字符流中获取宽字符，对应 fgetc()。
- fgetws()	从宽字符流中读取宽字符串，对应 fgets()。
- fputwc()	将宽字符写入宽字符流，对应 fputc()。
- fputws()	将宽字符串写入宽字符流，对应 fputs()。
- fwprintf()	格式化宽输出到宽字符流，对应 fprintf()。
- fwscanf()	来自宽字符流的格式化宽字符输入，对应 fscanf()。
- getwchar()	从 stdin 获取一个宽字符，对应 getchar()。
- getwc()	从 stdin 获取一个宽字符，对应 getc()。
- putwchar()	写一个宽字符到 stdout，对应 putchar()。
- putwc()	写一个宽字符到 stdout，对应 putc()。
- swprintf()	格式化宽输出到宽字符串，对应 sprintf()。
- swscanf()	来自宽字符串的格式化宽输入，对应 sscanf()。
- ungetwc()	将宽字符推回输入流，对应 ungetc()。
- vfwprintf()	可变参数的格式化宽字符输出到宽字符流，对应 vfprintf()。
- vfwscanf()	来自宽字符流的可变参数格式化宽字符输入，对应 vfscanf()。
- vswprintf()	可变参数的格式化宽字符输出到宽字符串，对应 vswprintf()。
- vswscanf()	来自宽字符串的可变参数格式化宽字符输入，对应 vsscanf()。
- vwprintf()	可变参数格式化宽字符输出，对应 vprintf()。
- vwscanf()	可变参数的格式化宽字符输入，对应 vscanf()。
- wcscat()	危险地连接宽字符串，对应 strcat()。
- wcschr()	在宽字符串中查找宽字符，对应 strchr()。
- wcscmp()	比较宽字符串，对应 strcmp()。
- wcscoll()	比较两个考虑语言环境的宽字符串，对应 strcoll()。
- wcscpy()	危险地复制宽字符串，对应 strcpy()。
- wcscspn()	不是从宽字符串前面开始计算字符，对应 strcspn()。
- wcsftime()	格式化的日期和时间输出，对应 strftime()。
- wcslen()	返回宽字符串的长度，对应 strlen()。
- wcsncat()	更安全地连接宽字符串，对应 strncat()。
- wcsncmp()	比较宽字符串，长度有限，对应 strncmp()。
- wcsncpy()	更安全地复制宽字符串，对应 strncpy()。
- wcspbrk()	在宽字符串中搜索一组宽字符中的一个，对应 strpbrk()。
- wcsrchr()	从末尾开始在宽字符串中查找宽字符，对应 strrchr()。
- wcsspn()	从宽字符串前面的集合中计算字符，对应 strspn()。
- wcsstr()	在另一个宽字符串中找到一个宽字符串，对应 strstr()。
- wcstod()	将宽字符串转换为 double，对应 strtod()。
- wcstof()	将宽字符串转换为 float，对应 strtof()。
- wcstok()	标记一个宽字符串，对应 strtok()。
- wcstold()	将宽字符串转换为 long double，对应 strtold()。
- wcstoll()	将宽字符串转换为 long long，对应 strtoll()。
- wcstol()	将宽字符串转换为 long，对应 strtol()。
- wcstoull()	将宽字符串转换为 unsigned long long，对应 strtoull()。
- wcstoul()	将宽字符串转换为 unsigned long，对应 strtoul()。
- wcsxfrm()	转换宽字符串以根据语言环境进行比较，对应 strxfrm()。
- wmemcmp()	比较内存中的宽字符，对应 memcmp()。
- wmemcpy()	复制宽字符内存，对应 memcpy()。
- wmemmove()	复制宽字符内存，可能重叠，对应 memmove()。
- wprintf()	格式化宽输出，对应 printf()。
- wscanf()	格式化宽输入，对应 scanf()。

## 多字节字符专用函数

wchar.h 也定义了一些多字节字符的专用函数。

- mbsinit() 判断 mbstate_t 是否处于初始转换状态。
- mbrlen()	给定转换状态时，计算多字节字符串的字节数，对应 mblen()。
- mbrtowc()	给定转换状态时，将多字节字符转换为宽字符，对应 mbtowc()。
- wctombr()	给定转换状态时，将宽字符转换为多字节字符，对应 wctomb()。
- mbsrtowcs()	给定转换状态时，将多字节字符串转换为宽字符串，对应 mbstowcs()。
- wcsrtombs() 给定转换状态时，将宽字符串转换为多字节字符串，对应 wcstombs()。
