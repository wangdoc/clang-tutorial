# stdio.h

`stdio.h`是 C 语言的标准 I/O 库，用于读取和写入文件，也用于控制台的输入和输出。

## 标准 I/O 函数

以下函数用于控制台的输入和输出。

- printf()：输出到控制台，详见《基本语法》一章。
- scanf()：从控制台读取输入，详见《I/O 函数》一章。
- getchar()：从控制台读取一个字符，详见《I/O 函数》一章。
- putchar()：向控制台写入一个字符，详见《I/O 函数》一章。
- gets()：从控制台读取整行输入（已废除），详见《I/O 函数》一章。
- puts()：向控制台写入一个字符串，详见《I/O 函数》一章。

## 文件操作函数

以下函数用于文件操作，详见《文件操作》一章。

- fopen()：打开文件。
- fclose()：关闭文件。
- freopen()：打开一个新文件，关联一个已经打开的文件指针。
- fprintf()：输出到文件。
- fscanf()：从文件读取数据。
- getc()：从文件读取一个字符。
- fgetc()：从文件读取一个字符。
- putc()：向文件写入一个字符。
- fputc()：向文件写入一个字符。
- fgets()：从文件读取整行。
- fputs()：向文件写入字符串。
- fread()：从文件读取二进制数据。
- fwrite()：向文件写入二进制数据。
- fseek()：将文件内部指针移到指定位置。
- ftell()：获取文件内部指针的当前位置。
- rewind()：将文件内部指针重置到文件开始处。
- fgetpos()：获取文件内部指针的当前位置。
- fsetpos()：设置文件内部指针的当前位置。
- feof()：判断文件内部指针是否指向文件结尾。
- ferror()：返回文件错误指示器的状态。
- clearerr()：重置文件错误指示器。
- remove()：删除文件。
- rename()：文件改名，以及移动文件。

## 字符串操作函数

以下函数用于操作字符串，详见《字符串操作》一章。

- sscanf()：从字符串读取数据，详见《I/O 函数》一章。
- sprintf()：输出到字符串。
- snprintf()：输出到字符串的更安全版本，指定了输出字符串的数量。

## tmpfile()

`tmpfile()`函数创建一个临时文件，该文件只在程序运行期间存在，除非手动关闭它。它的原型如下。

```c
FILE* tmpfile(void);
```

`tmpfile()`返回一个文件指针，可以用于访问该函数创建的临时文件。如果创建失败，返回一个空指针 NULL。

```c
FILE* tempptr;
tempptr = tmpfile();
```

调用`close()`方法关闭临时文件后，该文件将被自动删除。

`tmpfile()`有两个缺点。一是无法知道临时文件的文件名，二是无法让该文件成为永久文件。

## tmpnam()

`tmpname()`函数为临时文件生成一个名字，确保不会与其他文件重名。它的原型如下。

```c
char* tmpname(char* s);
```

它的参数是一个字符串变量，`tmpnam()`会把临时文件的文件名复制到这个变量里面，并返回指向该字符串变量的指针。如果生成文件名失败，`tmpnam()`返回空指针 NULL。

```c
char filename[L_tmpnam];

if (tmpnam(filename) != NULL)
  // 输出诸如 /tmp/filew9PMuZ 的文件名
  printf("%s\n", filename);
else
  printf("Something wrong!\n");
```

上面示例中，`L_tmpnam`是`stdio.h`定义的一个宏，指定了临时文件的文件名长度。

`tmpname()`的参数也可以是一个空指针 NULL，同样返回指向文件名字符串的指针。

```c
char* filename;
filename = tmpnam(NULL);
```

上面示例中，变量`filename`就是`tmpnam()`生成的文件名。

该函数只是生成一个文件名，稍后可以使用`fopen()`打开该文件并使用它。

## fflush()

`fflush()`用于清空缓存区。它接受一个文件指针作为参数，将缓存区内容写入该文件。

```c
fflush(fp);
```

如果不需要保存缓存区内容，则可以传入空指针 NULL。

```c
fflush(NULL);
```

如果清空成功，`fflush()`返回0，否则返回 EOF。

注意，`fflush()`一般只用来清空输出缓存区（比如写文件）。如果使用它来清空输入缓存区（比如读文件），属于未定义行为。

`fflush()`的一个用途是不等回车键，就强迫输出缓存区。大多数系统都是行缓存，这意味着只有遇到回车键（或者缓存区满了，或者文件读到结尾），缓存区的内容才会输出，`fflush()`可以不等回车键，立即输出。

```c
for (int i = 9; i >= 0; i--) {
  printf("\r%d", i);
  fflush(stdout);
  sleep(1);
}
```

上面示例是一个倒计时效果，`\r`是回车键，表示每轮循环都会回到当前行的行首，等于删除上一轮循环的输出。`fflush(stdout)`表示立即将缓存输出到显示器，这一行是必需的，否则由于上一行的输出没有回车键，不会触发缓存输出，屏幕上不会显示任何内容，只会等到程序运行结束再一次性输出。

## setvbuf()

`setvbuf()`函数用于定义某个字节流应该如何缓存。它可以接受四个参数。

```c
int setvbuf(FILE* stream, char* buffer, int mode, size_t size)
```

第一个参数`stream`是文件流。

第二个参数`buffer`是缓存区的地址。

第三个参数`mode`指定缓存的行为模式，它是下面三个宏之一，这些宏都定义在`stdio.h`。

- `_IOFBF`：满缓存。当缓存为空时，才从流读入数据；当缓存满了，才向流写入数据。一般情况下，这是默认设置。
- `_IOLBF`：行缓存。每次从流读入一行数据，或向流写入一行数据，即以行为单位读写缓存。
- `_IONBF`：无缓存。不使用缓存区，直接读写设备。

第四个参数`size`指定缓存区的大小。较大的缓存区提供更好的性能，而较小的缓存区可以节省空间。`stdio.h`提供了一个宏`BUFSIZ`，表示系统默认的缓存区大小。

它的意义在于，使得用户可以在打开一个文件之前，定义自己的文件缓冲区，而不必使用`fopen()`函数打开文件时设定的默认缓冲区。

```c
char buffer[N];

setvbuf(stream, buffer, _IOFBF, N);
```

上面示例设置文件流`stream`的缓存区从地址`buffer`开始，大小为`N`，模式为`_IOFBF`。

`setvbuf()`的第二个参数可以为空指针 NULL。这样的话，`setvbuf()`会自己创建一个缓存区。

注意，`setvbuf()`的调用必须在对文件流执行任何操作之前。

如果调用成功，`setvbuf()`的返回值为`0`，否则返回非零值。

下面的例子是将缓存区调整为行缓存。

```c
FILE *fp;
char lineBuf[1024];

fp = fopen("somefile.txt", "r");
setvbuf(fp, lineBuf, _IOLBF, 1024);
```

## setbuf()

`setbuf()`是`setvbuf()`的早期版本，可以视为后者的简化版本，也用来定义某个字节流的缓存区。

```c
void setbuf(FILE* stream, char* buffer);
```

它的第一个参数`stream`是文件流，第二个参数`buffer`是缓存区的地址。

它总是可以改写成`setvbuf()`。

```c
char buffer[BUFSIZ];

setbuf(stream, buffer);

// 等同于
setvbuf(stream, buffer, _IOFBF, BUFSIZ);
```

上面示例中，`BUFSIZ`是`stdio.h`定义的宏，表示系统默认的缓存区大小。

`setbuf()`函数没有返回值。

`setbuf()`的第二个参数如果设置为 NULL，表示不进行缓存。

```c
setbuf(stdout, NULL);

// 等同于
setvbuf(stdout, NULL, _IONBF, 0);
```

## ungetc()

`ungetc()`将从缓存里面读取的上一个字符，重新放回缓存，下一个读取缓存的操作会从这个字符开始。有些操作需要了解下一个字符是什么，再决定应该怎么处理，这时这个函数就很有用。

它的原型如下。

```c
int ungetc(int c, FILE *stream);
```

它的第一个参数是一个字符变量，第二个参数是一个打开的文件流。它的返回值是放回缓存的那个字符，操作失败时，返回 EOF。

```c
int ch = fgetc(fp);

if (isdigit(ch)) {
  ch = fgetc(fp);
}

ungetc(ch, fp);
```

上面示例中，如果读取的字符不是数字，就将其放回缓存。

## perror()

`perror()`用于在 stderr 的错误信息之前，添加一个自定义字符串。

```c
void perror(const char *s);
```

该函数的参数就是在报错信息前添加的字符串。它没有返回值。

```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <errno.h>

int main(void) {
  int x = -1;

  errno = 0;
  float y = sqrt(x);
  if (errno != 0) {
    perror("sqrt error");
    exit(EXIT_FAILURE);
  }
}
```

上面示例中，求`-1`的平方根，导致报错。头文件`errno.h`提供宏`errno`，只要上一步操作出错，这个宏就会设置成非零值。`perror()`用来在报错信息前，加上`sqrt error`的自定义字符串。

执行上面的程序，就会得到下面的报错信息。

```bash
$ gcc test.c -lm
$ ./a.out
sqrt error: Numerical argument out of domain
```

## 可变参数操作函数

（1）输出函数

下面是`printf()`的变体函数，用于按照给定格式，输出函数的可变参数列表（va_list）。

- vprintf()：按照给定格式，输出到控制台，默认是显示器。
- vfprintf()：按照给定格式，输出到文件。
- vsprintf()：按照给定格式，输出到字符串。
- vsnprintf()：按照给定格式，输出到字符串的安全版本。

它们的原型如下，基本与对应的`printf()`系列函数一致，除了最后一个参数是可变参数对象。

```c
#include <stdio.h>
#include <stdarg.h>
    
int vprintf(
  const char * restrict format,
  va_list arg
);

int vfprintf(
  FILE * restrict stream,
  const char * restrict format,
  va_list arg
);
    
int vsprintf(
  char * restrict s,
  const char * restrict format,
  va_list arg
);
    
int vsnprintf(
  char * restrict s,
  size_t n,
  const char * restrict format,
  va_list arg
);
```

它们的返回值都为输出的字符数，如果出错，返回负值。

`vsprintf()`和`vsnprintf()`的第一个参数可以为 NULL，用来查看多少个字符会被写入。

下面是一个例子。

```c
int logger(char *format, ...) {
  va_list va;
  va_start(va, format);
  int result = vprintf(format, va);
  va_end(va);

  printf("\n");

  return result;
}

// 输出 x = 12 and y = 3.20
logger("x = %d and y = %.2f", x, y);
```

（2）输入函数

下面是`scanf()`的变体函数，用于按照给定格式，输入可变参数列表 (va_list)。

- vscanf()：按照给定格式，从控制台读取（默认为键盘）。
- vfscanf()：按照给定格式，从文件读取。
- vsscanf()：按照给定格式，从字符串读取。

它们的原型如下，跟对应的`scanf()`函数基本一致，除了最后一个参数是可变参数对象。

```c
#include <stdio.h>
#include <stdarg.h>
    
int vscanf(
  const char * restrict format,
  va_list arg
);
    
int vfscanf(
  FILE * restrict stream,
  const char * restrict format,
  va_list arg
);
    
int vsscanf(
  const char * restrict s,
  const char * restrict format,
  va_list arg
);
```

它们返回成功读取的项数，遇到文件结尾或错误，则返回 EOF。

下面是一个例子。

```c
int error_check_scanf(int expected_count, char *format, ...) {
  va_list va;

  va_start(va, format);
  int count = vscanf(format, va);
  va_end(va);

  assert(count == expected_count);

  return count;
}

error_check_scanf(3, "%d, %d/%f", &a, &b, &c);
```
