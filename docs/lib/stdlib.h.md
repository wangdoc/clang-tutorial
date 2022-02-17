# stdlib.h

## 类型别名和宏

stdlib.h 定义了下面的类型别名。

- size_t：sizeof 的返回类型。
- wchar_t：宽字符类型。

stdlib.h 定义了下面的宏。

- NULL：空指针。
- EXIT_SUCCESS：函数运行成功时的退出状态。
- EXIT_FAILURE：函数运行错误时的退出状态。
- RAND_MAX：rand() 函数可以返回的最大值。
- MB_CUR_MAX：当前语言环境中，多字节字符占用的最大字节数。

## abs()，labs()，llabs()

这三个函数用于计算整数的绝对值。`abs()`用于 int 类型，`labs()`用于 long int 类型，`llabs()`用于 long long int 类型。

```c
int abs(int j);
long int labs(long int j);
long long int llabs(long long int j);
```

下面是用法示例。

```c
// 输出 |-2| = 2
printf("|-2| = %d\n", abs(-2));

// 输出 |4|  = 4
printf("|4|  = %d\n", abs(4));
```

## div()，ldiv()，lldiv()

这三个函数用来计算两个参数的商和余数。`div()`用于 int 类型的相除，`ldiv()`用于 long int 类型的相除，`lldiv()`用于 long long int 类型的相除。

```c
div_t div(int numer, int denom);
ldiv_t ldiv(long int numer, long int denom);
lldiv_t lldiv(long long int numer, long long int denom);
```

这些函数把第2个参数（分母）除以第1个参数（分子），产生商和余数。这两个值通过一个数据结构返回，`div()`返回 div_t 结构，`ldiv()`返回 ldiv_t 结构，`lldiv()`返回 lldiv_t 结构。

这些结构都包含下面两个字段，

```c
int　quot;　 //　商
int　rem;　 //　余数
```

它们完整的定义如下。

```c
typedef struct {
  int quot, rem;
} div_t;
    
typedef struct {
  long int quot, rem;
} ldiv_t;
    
typedef struct {
  long long int quot, rem;
} lldiv_t;
```

下面是一个例子。

```c
div_t d = div(64, -7);

// 输出 64 / -7 = -9
printf("64 / -7 = %d\n", d.quot);

// 输出 64 % -7 = 1
printf("64 %% -7 = %d\n", d.rem);
```

## 字符串转成数值

### a 系列函数

`stdlib.h`定义了一系列函数，可以将字符串转为数字。

- atoi()：字符串转成 int 类型。
- atof()：字符串转成 double 类型。
- atol()：字符串转成 long int 类型。
- atoll()：字符串转成 long long int 类型。

它们的原型如下。

```c
int atoi(const char* nptr);
double atof(const char* nptr);
long int atol(const char* nptr);
long long int atoll(const char* nptr);
```

上面函数的参数都是一个字符串指针，字符串开头的空格会被忽略，转换到第一个无效字符处停止。函数名称里面的`a`代表 ASCII，所以`atoi()`的意思是“ASCII to int”。

它们返回转换后的数值，如果字符串无法转换，则返回`0`。

下面是用法示例。

```c
atoi("3490")   // 3490
atof("3.141593")   // 3.141593
```

如果参数是数字开头的字符串，`atoi()`会只转换数字部分，比如`atoi("42regular")`会返回整数`42`。如果首字符不是数字，比如“hello world”，则会返回`0`。

### str 系列函数（浮点数转换）

`stdlib.h`还定义了一些更强功能的浮点数转换函数。

- strtof()：字符串转成 float 类型。
- strtod()：字符串转成 double 类型。
- strtold()：字符串转成 long double 类型。

它们的原型如下。

```c
float strtof(
  const char* restrict nptr,
  char** restrict endptr
);

double strtod(
  const char* restrict nptr,
  char** restrict endptr
);
    
long double strtold(
  const char* restrict nptr,
  char** restrict endptr
);
```

它们都接受两个参数，第一个参数是需要转换的字符串，第二个参数是一个指针，指向原始字符串里面无法转换的部分。

- `nptr`：待转换的字符串（起首的空白字符会被忽略）。
- `endprt`：一个指针，指向不能转换部分的第一个字符。如果字符串可以完全转成数值，该指针指向字符串末尾的终止符`\0`。这个参数如果设为 NULL，就表示不需要处理字符串剩余部分。

它们的返回值是已经转换后的数值。如果字符串无法转换，则返回`0`。如果转换结果发生溢出，errno 会被设置为 ERANGE。如果值太大（无论是正数还是负数），函数返回`HUGE_VAL`；如果值太小，函数返回零。

```c
char *inp = "   123.4567abdc";
char *badchar;

double val = strtod(inp, &badchar);

printf("%f\n", val); // 123.456700
printf("%s\n", badchar); // abdc
```

字符串可以完全转换的情况下，第二个参数指向`\0`，因此可以用下面的写法判断是否完全转换。

```c
if (*endptr == '\0') {
  // 完全转换
} else {
  // 存在无法转换的字符
}
```

如果不关心没有转换的部分，则可以将 endptr 设置为 NULL。

这些函数还可以将字符串转换为特殊值 Infinity 和 NaN。如果字符串包含 INF 或 INFINITY（大写或小写皆可），则将转换为 Infinity；如果字符串包含 NAN，则将返回 NaN。

### str 系列函数（整数转换）

str 系列函数也有整数转换的对应函数。

- strtol()：字符串转成 long int 类型。
- strtoll()：字符串转成 long long int 类型。
- strtoul()：字符串转成 unsigned long int 类型。
- strtoull()：字符串转成 unsigned long long int 类型。

它们的原型如下。

```c
long int strtol(
  const char* restrict nptr,
  char** restrict endptr,
  int base
);
    
long long int strtoll(
  const char* restrict nptr,
  char** restrict endptr,
  int base
);
    
unsigned long int strtoul(
  const char* restrict nptr,
  char** restrict endptr,
  int base
);
    
unsigned long long int strtoull(
  const char* restrict nptr,
  char** restrict endptr, int base
);
```

它们接受三个参数。

（1）`nPtr`：待转换的字符串（起首的空白字符会被忽略）。

（2）`endPrt`：一个指针，指向不能转换部分的第一个字符。如果字符串可以完全转成数值，该指针指向字符串末尾的终止符`\0`。这个参数如果设为 NULL，就表示不需要处理字符串剩余部分。

（3）`base`：待转换整数的进制。这个值应该是`2`到`36`之间的整数，代表相应的进制，如果是特殊值`0`，表示让函数根据数值的前缀，自己确定进制，即如果数字有前缀`0`，则为八进制，如果数字有前缀`0x`或`0X`，则为十六进制。

它们的返回值是转换后的数值，如果转换不成功，返回`0`。

下面是转换十进制整数的例子。

```c
char* s = "3490";
unsigned long int x = strtoul(u, NULL, 10);

printf("%lu\n", x); // 3490
```

下面是转换十六进制整数的例子。

```c
char* end;

long value = strtol("0xff", &end, 16);
printf("%ld\n", value); // 255
printf("%s\n", end); // 无内容

value = strtol("0xffxx", &end, 16);
printf("%ld\n", value); // 255
printf("%s\n", end); // xx
```

上面示例中，`strtol()`可以指定字符串包含的是16进制整数。不能转换的部分，可以使用指针`end`进行访问。

下面是转换二进制整数的例子。

```c
char* s = "101010";
unsigned long int x = strtoul(s, NULL, 2);

printf("%lu\n", x); // 42
```

下面是让函数自行判断整数进制的例子。

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
  const char* string = "-1234567abc";
  char* remainderPtr;
  long x = strtol(string, &remainderPtr, 0);
  printf("%s\"%s\"\n%s%ld\n%s\"%s\"\n",
    "The original string is ",
    string,
    "The converted value is ",
    x,
    "The remainder of the original string is ",
    remainderPtr
  );
}
```

上面代码的输出结果如下。

```c
The original string is "-1234567abc"
The converted value is -1234567
The remainder of the original string is "abc"
```

如果被转换的值太大，`strtol()`函数在`errno`中存储`ERANGE`这个值，并返回`LONG_MIN`（原值为负数）或`LONG_MAX`（原值为正数），`strtoul()`则返回`ULONG_MAX`。

## rand()

`rand()`函数用来生成 0～RAND_MAX 之间的随机整数。`RAND_MAX`是一个定义在`stdlib.h`里面的宏，通常等于 INT_MAX。

```c
// 原型
int rand(void);

// 示例
int x = rand();
```

如果希望获得整数 N 到 M 之间的随机数（包括 N 和 M 两个端点值），可以使用下面的写法。

```c
int x = rand() % （M - N + 1) + N;
```

比如，1 到 6 之间的随机数，写法如下。

```c
int x = rand() % 6 + 1;
```

获得浮点数的随机值，可以使用下面的写法。

```c
// 0 到 0.999999 之间的随机数
printf("0 to 0.99999: %f\n", rand() / ((float)RAND_MAX + 1));

// n 到 m 之间的随机数：
// n + m * (rand() / (float)RAND_MAX)
printf("10.5 to 15.7: %f\n", 10.5 + 5.2 * rand() / (float)RAND_MAX);
```

上面示例中，由于`rand()`和`RAND_MAX`都是 int 类型，要用显示的类型转换转为浮点数。

## srand()

`rand()`是伪随机数函数，为了增加随机性，必须在调用它之前，使用`srand()`函数重置一下种子值。

`srand()`函数接受一个无符号整数（unsigned int）作为种子值，没有返回值。

```c
void srand(unsigned int seed);
```

通常使用`time(NULL)`函数返回当前距离时间纪元的秒数，作为`srand()`的参数。

```c
#include <time.h>
srand((unsigned int) time(NULL));
```

上面代码中，`time()`的原型定义在头文件`time.h`里面，返回值的类型是类型别名`time_t`，具体的类型与系统有关，所以要强制转换一下类型。`time()`的参数是一个指针，指向一个具体的 time_t 类型的时间值，这里传入空指针`NULL`作为参数，由于 NULL 一般是`0`，所以也可以写成`time(0)`。

## abort()

`abort()`用于不正常地终止一个正在执行的程序。使用这个函数的目的，主要是它会触发 SIGABRT 信号，开发者可以在程序中为这个信号设置一个处理函数。

```c
void abort(void);
```

该函数没有参数。

## exit()，quick_exit()，_Exit()

这三个函数都用来退出当前正在执行的程序。

```c
void exit(int status);
void quick_exit(int status);
void _Exit(int status);
```

它们都接受一个整数，表示程序的退出状态，`0`是正常退出，非零值表示发生错误，可以使用宏`EXIT_SUCCESS`和`EXIT_FAILURE`当作参数。它们本身没有返回值。

它们的区别是，退出时所做的清理工作不同。`exit()`是正常退出，系统会做完整的清理，比如更新所有文件流，并且删除临时文件。`quick_exit()`是快速退出，系统的清理工作稍微少一点。`_Exit()`是立即退出，不做任何清理工作。

下面是一些用法示例。

```c
exit(EXIT_SUCCESS);
quick_exit(EXIT_FAILURE);
_Exit(2);
```

## atexit()，at_quick_exit()

`atexit()`用来登记当前程序退出时（调用`exit()`或`main()`正常退出），所要执行的其他函数。

`at_quick_exit()`则是登记使用`quick_exit()`方法退出当前程序时，所要执行的其他函数。

`exit()`只能触发`atexit()`登记的函数，`quick_exit()`只能触发`at_quick_exit()`登记的函数。

```c
int atexit(void (*func)(void));
int at_quick_exit(void (*func)(void));
```

它们的参数是要执行的函数地址，即函数名。它们的返回值都是调用成功时返回`0`，调用失败时返回非零值。

下面是一个例子。

```c
void sign_off(void);
void too_bad(void);

int main(void) {

  int n;
  atexit(sign_off);　　 /* 注册 sign_off()函数 */

  puts("Enter an integer:");
  if (scanf("%d", &n) != 1) {
    puts("That's no integer!");
    atexit(too_bad);　/* 注册 too_bad()函数 */
    exit(EXIT_FAILURE);
  }

  printf("%d is %s.\n", n, (n % 2 == 0) ? "even" : "odd");
  return 0;
}

void sign_off(void) {
  puts("sign_off");
}

void too_bad(void) {
  puts("too bad");
}
```

上面示例中，用户输入失败时，会调用`sign_off()`和`too_bad()`函数；但是输入成功时只会调用`sign_off()`。因为只有输入失败时，才会进入`if`语句登记`too_bad()`。

另外，如果有多条`atexit()`语句，函数退出时最先调用的，是最后一个登记的函数。

`atexit()`登记的函数（如上例的`sign_off`和`too_bad`）应该不带任何参数且返回类型为`void`。通常，这些函数会执行一些清理任务，例如删除临时文件或重置环境变量。

`at_quick_exit()`也是同样的规则，下面是一个例子。

```c
void exit_handler_1(void) {
  printf("1\n");
}

void exit_handler_2(void) {
  printf("2\n");
}

int main(void) {
  at_quick_exit(exit_handler_1);
  at_quick_exit(exit_handler_2);
  quick_exit(0);
}
```

执行上面的示例，命令行会先输出2，再输出1。

## getenv()

`getenv()`用于获取环境变量的值。环境变量是操作系统提供的程序之外的一些环境参数。

```c
char* getenv(const char* name);
```

它的参数是一个字符串，表示环境变量名。返回值也是一个字符串，表示环境变量的值。如果指定的环境变量不存在，则返回 NULL。

下面是输出环境变量`$PATH`的值的例子。

```c
printf("PATH is %s\n", getenv("PATH"));
```

## system()

`system()`函数用于执行外部程序。它会把它的参数字符串传递给操作系统，让操作系统的命令处理器来执行。

```c
void system( char const * command );
```

这个函数的返回值因编译器而异。但是标准规定，如果 NULL 作为参数，表示询问操作系统，是否有可用的命令处理器，如果有的话，返回一个非零值，否则返回零。

下面是执行`ls`命令的例子。

```c
system("ls -l"); 
```

## 内存管理函数

stdlib.h 提供了一些内存操作函数，下面几个函数详见《内存管理》一章，其余在本节介绍。

- malloc()：分配内存区域
- calloc()：分配内存区域。
- realloc()：调节内存区域大小。
- free()：释放内存区域。

### aligned_alloc()

很多系统有内存对齐的要求，即内存块的大小必须是某个值（比如64字节）的倍数，这样有利于提高处理速度。`aligned_alloc()`就用于分配满足内存对齐要求的内存块，它的原型如下。

```c
void* aligned_alloc(size_t alignment, size_t size);
```

它接受两个参数。

- alignment：整数，表示内存对齐的单位大小，一般是2的整数次幂（2、4、8、16……）。
- size：整数，表示内存块的大小。

分配成功时，它返回一个无类型指针，指向新分配的内存块。分配失败时，返回 NULL。

```c
char* p = aligned_alloc(64, 256);
```

上面示例中，`aligned_alloc()`分配的内存块，单位大小是64字节，要分配的字节数是256字节。

## qsort()

`qsort()`用来快速排序一个数组。它对数组成员的类型没有要求，任何类型数组都可以用这个函数排序。

```c
void qsort(
  void *base,
  size_t nmemb, 
  size_t size,
  int (*compar)(const void *, const void *)
);
```

该函数接受四个参数。

- base：指向要排序的数组开始位置的指针。
- nmemb：数组成员的数量。
- size：数组每个成员占用的字节长度。
- compar：一个函数指针，指向一个比较两个成员的函数。

比较函数`compar`将指向数组两个成员的指针作为参数，并比较两个成员。如果第一个参数小于第二个参数，该函数应该返回一个负值；如果两个函数相等，返回`0`；如果第一个参数大于第二个参数，应该返回一个正数。

下面是一个用法示例。

```c
#include <stdio.h>
#include <stdlib.h>

int compar(const void* elem0, const void* elem1) {
  const int* x = elem0;
  const int* y = elem1; 
  
  return *x - *y;
}

int main(void) {
  int a[9] = {14, 2, 3, 17, 10, 8, 6, 1, 13};

  qsort(a, 9, sizeof(int), compar);

  for (int i = 0; i < 9; i++)
    printf("%d ", a[i]);
  putchar('\n');
}
```

执行上面示例，会输出排序好的数组“1 2 3 6 8 10 13 14 17”。

## bsearch()

`bsearch()`使用二分法搜索，在数组中搜索一个值。它对数组成员的类型没有要求，任何类型数组都可以用这个函数搜索值。

注意，该方法只对已经排序好的数组有效。

```c
void *bsearch(
  const void* key,
  const void* base,
  size_t nmemb,
  size_t size,
  int (*compar)(const void *, const void *)
);
```

这个函数接受5个参数。

- key：指向要查找的值的指针。
- base：指向数组开始位置的指针，数组必须已经排序。
- nmemb：数组成员的数量。
- size：数组每个成员占用的字节长度。
- compar：指向一个将待查找值与其他值进行比较的函数的指针。

比较函数`compar`将待查找的值作为第一个参数，将要比较的值作为第二个参数。如果第一个参数小于第二个参数，该函数应该返回一个负值；如果两个参数相等，返回`0`；如果第一个参数大于第二个参数，返回一个正值。

如果找到待查找的值，`bsearch()`返回指向该值的指针，如果找不到，返回 NULL。

下面是一个用法示例。

```c
#include <stdio.h>
#include <stdlib.h>

int compar(const void *key, const void *value) {
  const int* k = key;
  const int* v = value;

  return *k - *v;
}

int main(void) {
  int a[9] = {2, 6, 9, 12, 13, 18, 20, 32, 47};

  int* r;
  int key;

  key = 12; // 包括在数组中
  r = bsearch(&key, a, 9, sizeof(int), compar);
  printf("Found %d\n", *r);

  key = 30;  // 不包括在数组中
  r = bsearch(&key, a, 9, sizeof(int), compar);
  if (r == NULL)
    printf("Didn't find 30\n");

  return 0;
}
```

执行上面的示例，会输出下面的结果。

```bash
Found 12
Didn't find 30
```

## 多字节字符函数

stdlib.h 提供了下面的函数，用来操作多字节字符，详见《多字节字符》一章。

- mblen()：多字节字符的字节长度。
- mbtowc()：将多字节字符转换为宽字符。
- wctomb()：将宽字符转换为多字节字符。
- mbstowcs()：将多字节字符串转换为宽字符串。
- wcstombs()：将宽字符串转换为多字节字符串。
