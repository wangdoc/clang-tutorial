# string.h

`string.h`主要定义了字符串处理函数和内存操作函数。

## 字符串处理函数

以下字符串处理函数，详见《字符串》一章。

- strcpy()：复制字符串。
- strncpy()：复制字符串，有长度限制。
- strcat()：连接两个字符串。
- strncat()：连接两个字符串，有长度限制。
- strcmp()：比较两个字符串。
- strncmp()：比较两个字符串，有长度限制。
- strlen()：返回字符串的字节数。

### strchr()，strrchr()

`strchr()`和`strrchr()`都用于在字符串中查找指定字符。不同之处是，`strchr()`从字符串开头开始查找，`strrchr()`从字符串结尾开始查找，函数名里面多出来的那个`r`表示 reverse（反向）。

```c
char* strchr(char* str, int c);
char* strrchr(char *str, int c);
```

它们都接受两个参数，第一个参数是字符串指针，第二个参数是所要查找的字符。

一旦找到该字符，它们就会停止查找，并返回指向该字符的指针。如果没有找到，则返回 NULL。

下面是一个例子。

```c
char *str = "Hello, world!";
char *p;

p = strchr(str, ',');  // p 指向逗号的位置
p = strrchr(str, 'o'); // p 指向 world 里面 o 的位置
```

### strspn()，strcspn()

`strspn()`用来查找属于指定字符集的字符串长度，`strcspn()`正好相反，用来查找不属于指定字符集的字符串长度。

```c
size_t strspn(char* str, const char* accept);
size_t strcspn(char *str, const char *reject);
```

这两个函数接受两个参数，第一个参数是源字符串，第二个参数是由指定字符组成的字符串。

`strspn()`从第一个参数的开头开始查找，一旦发现第一个不属于指定字符集范围的字符，就停止查找，返回到目前为止的字符串长度。如果始终没有不在指定字符集的字符，则返回第一个参数字符串的长度。

`strcspn()`则是一旦发现第一个属于指定字符集范围的字符，就停止查找，返回到目前为止的字符串长度。如果始终没有发现指定字符集的字符，则返回第一个参数字符串的长度。

```c
char str[] = "hello world";
int n;

n = strspn(str1, "aeiou");
printf("%d\n", n);  // n == 0

n = strcspn(str1, "aeiou");
printf("%d\n", n); // n == 1
```

上面示例中，第一个`n`等于0，因为0号位置的字符`h`就不属于指定字符集`aeiou`，可以理解为开头有0个字符属于指定字符集。第二个`n`等于1，因为1号位置的字符`e`属于指定字符集`aeiou`，可以理解为开头有1个字符不属于指定字符集。

### strpbrk()

`strpbrk()`在字符串中搜索指定字符集的任一个字符。

```c
char* strpbrk(const char* s1, const char* s2);
```

它接受两个参数，第一个参数是源字符串，第二个参数是由指定字符组成的字符串。

它返回一个指向第一个匹配字符的指针，如果未找到匹配字符，则返回 NULL。

```c
char* s1 = "Hello, world!";
char* s2 = "dow!";

char* p = strpbrk(s1, s2);

printf("%s\n", p);  // "o, world!"
```

上面示例中，指定字符集是“dow!”，那么`s1`里面第一个匹配字符是“Hello”的“o”，所以指针`p`指向这个字符。输出的话，就会输出从这个字符直到字符串末尾的“o, world!”。

### strstr()

`strstr()`在一个字符串里面，查找另一个字符串。

```c
char *strstr(
  const char* str,
  const char* substr
);
```

它接受两个参数，第一个参数是源字符串，第二个参数是所要查找的子字符串。

如果匹配成功，就返回一个指针，指向源字符串里面的子字符串。如果匹配失败，就返回 NULL，表示无法找到子字符串。

```c
char* str = "The quick brown fox jumped over the lazy dogs.";
char* p = strstr(str, "lazy");

printf("%s\n", p == NULL ? "null": p); // "lazy dogs."
```

上面示例中，`strstr()`用来在源字符串`str`里面，查找子字符串`lazy`。从返回的指针到字符串结尾，就是“lazy dogs.”。

### strtok()

`strtok()`用来将一个字符串按照指定的分隔符（delimiter），分解成一系列词元（tokens）。

```c
char* strtok(char* str, const char* delim);
```

它接受两个参数，第一个参数是待拆分的字符串，第二个参数是指定的分隔符。

它返回一个指针，指向分解出来的第一个词元，并将词元结束之处的分隔符替换成字符串结尾标志`\0`。如果没有待分解的词元，它返回 NULL。

如果要遍历所有词元，就必须循环调用，参考下面的例子。

`strtok()`的第一个参数如果是 NULL，则表示从上一次`strtok()`分解结束的位置，继续往下分解。

```c
#include <stdio.h>
#include <string.h>

int main(void) {
  char string[] = "This is a sentence with 7 tokens";
  char* tokenPtr = strtok(string, " ");

  while (tokenPtr != NULL) {
    printf("%s\n", tokenPtr);
    tokenPtr = strtok(NULL, " ");
  }
}
```

上面示例将源字符串按照空格，分解词元。它的输出结果如下。

```bash
This
is
a
sentence
with
7
tokens
```

注意，`strtok()`会修改原始字符串，将所有分隔符都替换成字符串结尾符号`\0`。因此，最好生成一个原始字符串的拷贝，然后再对这个拷贝执行`strtok()`。

### strcoll()

`strcoll()`用于比较两个启用了本地化设置的字符串，用法基本与`strcmp()`相同。

```c
int strcoll(const char *s1, const char *s2);
```

请看下面的示例。

```c
setlocale(LC_ALL, "");

// 报告 é > f
printf("%d\n", strcmp("é", "f"));  

// 报告 é < f
printf("%d\n", strcoll("é", "f"));
```

上面示例比较带重音符号的`é`与`f`，`strcmp()`会返回`é`大于`f`，而`strcoll()`就会正确识别`é`排在`f`前面，所以小于`f`。注意，在比较之前，需要使用`setlocale(LC_ALL, "")`，启用本地化设置。

### strxfrm()

`strxfrm()`将一个本地化字符串转成可以使用`strcmp()`进行比较的形式，相当于`strcoll()`内部的第一部分操作。

```c
size_t strxfrm(
  char * restrict s1, 
  const char * restrict s2, 
  size_t n
);
```

它接受三个参数，将第二个参数`s2`转为可以使用`strcmp()`比较的形式，并将结果存入第一个参数`s1`。第三个参数`n`用来限定写入的字符数，防止超出`s1`的边界。

它返回转换后的字符串长度，不包括结尾的终止符。

如果第一个参数是 NULL，第三个参数是0，则不进行实际的转换，只返回转换后所需的字符串长度。

下面的示例是用这个函数自己实现一个`strcoll()`。

```c
int my_strcoll(char* s1, char* s2) {
  int len1 = strxfrm(NULL, s1, 0) + 1;
  int len2 = strxfrm(NULL, s2, 0) + 1;

  char *d1 = malloc(len1);
  char *d2 = malloc(len2);

  strxfrm(d1, s1, len1);
  strxfrm(d2, s2, len2);

  int result = strcmp(d1, d2);

  free(d2);
  free(d1);

  return result;
}
```

上面示例中，先为两个进行比较的本地化字符串，分配转换后的存储空间，使用`strxfrm()`将它们转为可比较的形式，再用`strcmp()`进行比较。

### strerror()

`strerror()`函数返回特定错误的说明字符串。

```c
char *strerror(int errornum);
```

它的参数是错误的编号，由`errno.h`定义。返回值是一个指向说明字符串的指针。

```c
// 输出 No such file or directory
printf("%s\n", strerror(2));
```

上面示例输出2号错误的说明字符“No such file or directory“。

下面的例子是自定义报错信息。

```c
#include <stdio.h>
#include <string.h>
#include <errno.h>

int main(void) {
  FILE* fp = fopen("NONEXISTENT_FILE.TXT", "r");

  if (fp == NULL) {
    char* errmsg = strerror(errno);
    printf("Error %d opening file: %s\n", errno, errmsg);
  }
}
```

上面示例中，通过`strerror(errno)`拿到当前的默认报错信息，其中`errno`是`errno.h`定义的宏，表示当前的报错编号。然后，再输出一条自定义的报错信息。

## 内存操作函数

以下内存操作函数，详见《内存管理》一章。

- memcpy()：内存复制函数。
- memmove()：内存复制函数（允许重叠）。
- memcmp()：比较两个内存区域。

### memchr()

`memchr()`用于在内存区域中查找指定字符。

```c
void* memchr(const void* s, int c, size_t n);
```

它接受三个参数，第一个参数是内存区域的指针，第二个参数是所要查找的字符，第三个参数是内存区域的字节长度。

一旦找到，它就会停止查找，并返回指向该位置的指针。如果直到检查完指定的字节数，依然没有发现指定字符，则返回 NULL。

下面是一个例子。

```c
char *str = "Hello, world!";
char *p;

p = memchr(str, '!', 13); // p 指向感叹号的位置
```

### memset()

`memset()`将一段内存全部格式化为指定值。

```c
void* memset(void* s, int c, size_t n);
```

它的第一个参数是一个指针，指向内存区域的开始位置，第二个参数是待写入的字符值，第三个参数是一个整数，表示需要格式化的字节数。它返回第一个参数（指针）。

```c
memset(p, ' ', N);
```

上面示例中，p 是一个指针，指向一个长度为 N 个字节的内存区域。`memset()`将该块内存区域的每个字节，都改写为空格字符。

下面是另一个例子。

```c
char string1[15] = "BBBBBBBBBBBBBB";

// 输出 bbbbbbbBBBBBBB
printf("%s\n", (char*) memset(string1, 'b', 7));
```

`memset()`的一个重要用途，就是将数组成员全部初始化为0。

```c
memset(arr, 0, sizeof(arr));
```

下面是将 Struct 结构都初始化为0的例子。

```c
struct banana {
  float ripeness;
  char *peel_color;
  int grams;
};

struct banana b;

memset(&b, 0, sizeof b);

b.ripeness == 0.0;     // True
b.peel_color == NULL;  // True
b.grams == 0;          // True
```

上面示例，将 Struct banana 的实例 b 的所有属性都初始化为0。

## 其他函数

```c
void* memset(void* a, int c, size_t n);

size_t strlen(const char* s);
```
