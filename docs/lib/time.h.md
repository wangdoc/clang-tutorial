# time.h

## time_t

time_t 是一个表示时间的类型别名，可以视为国际标准时 UTC。它可能是浮点数，也可能是整数，Unix 系统一般是整数。

许多系统上，time_t 表示自时间纪元（time epoch）以来的秒数。Unix 的时间纪元是国际标准时 UTC 的1970年1月1日的零分零秒。time_t 如果为负数，则表示时间纪元之前的时间。

time_t 一般是32位或64位整数类型的别名，具体类型取决于当前系统。如果是32位带符号整数，time_t 可以表示的时间到 2038年1月19日03:14:07 UTC 为止；如果是32位无符号整数，则表示到2106年。如果是64位带符号整数，可以表示`-2930`亿年到`+2930`亿年的时间范围。

## struct tm

struct tm 是一个数据结构，用来保存时间的各个组成部分，比如小时、分钟、秒、日、月、年等。下面是它的结构。

```c
struct tm {
  int tm_sec;    // 秒数 [0, 60]
  int tm_min;    // 分钟 [0, 59]
  int tm_hour;   // 小时 [0, 23]
  int tm_mday;   // 月份的天数 [1, 31]
  int tm_mon;    // 月份 [0, 11]，一月用 0 表示
  int tm_year;   // 距离 1900 的年数
  int tm_wday;   // 星期几 [0, 6]，星期天用 0 表示
  int tm_yday;   // 距离1月1日的天数 [0, 365]
  int tm_isdst;  // 是否采用夏令时，1 表示采用，0 表示未采用
};
```

## time()

`time()`函数返回从时间纪元到现在经过的秒数。

```c
time_t time(time_t* returned_value);
```

`time()`接受一个 time_t 指针作为参数，返回值会写入指针地址。参数可以是空指针 NULL。

`time()`的返回值是 time_t 类型的当前时间。 如果计算机无法提供当前的秒数，或者返回值太大，无法用`time_t`类型表示，`time()`函数就返回`-1`。

```c
time_t now;

// 写法一    
now = time(NULL);

// 写法二    
time(&now);
```

上面示例展示了将当前时间存入变量`now`的两种写法。

如果要知道某个操作耗费的精确时间，需要调用两次`time()`，再将两次的返回值相减。

```c
time_t begin = time(NULL);

// ... 执行某些操作

time_t end = time(NULL);

printf("%d\n", end - begin);
```

注意，上面的方法只能精确到秒。

## ctime()

`ctime()`用来将 time_t 类型的值直接输出为人类可读的格式。

```c
char* ctime( time_t const * time_value );
```

`ctime()`的参数是一个 time_t 指针，返回一个字符串指针。该字符串的格式类似“Sun Jul 4 04:02:48 1976\n\0”，尾部包含换行符和字符串终止标志。

下面是一个例子。

```c
time_t now; 

now = time(NULL);

// 输出 Sun Feb 28 18:47:25 2021
printf("%s", ctime(&now));
```

注意，`ctime()`会在字符串尾部自动添加换行符。

## localtime()，gmtime()

`localtime()`函数用来将 time_t 类型的时间，转换为当前时区的 struct tm 结构。

`gmtime()`函数用来将 time_t 类型的时间，转换为 UTC 时间的 struct tm 结构。

它们的区别就是返回值，前者是本地时间，后者是 UTC 时间。

```c
struct tm* localtime(const time_t* timer);
struct tm* gmtime(const time_t* timer);
```

下面是一个例子。

```c
time_t now = time(NULL);

// 输出 Local: Sun Feb 28 20:15:27 2021
printf("Local: %s", asctime(localtime(&now)));

// 输出 UTC  : Mon Mar  1 04:15:27 2021
printf("UTC  : %s", asctime(gmtime(&now)));
```

## asctime()

`asctime()`函数用来将 struct tm 结构，直接输出为人类可读的格式。该函数会自动在输出的尾部添加换行符。

用法示例参考上一小节。

## mktime()

`mktime()`函数用于把一个 struct tm 结构转换为 time_t 值。

```c
time_t　mktime(struct tm* tm_ptr);
```

`mktime()`的参数是一个 struct tm 指针。

`mktime()`会自动设置 struct tm 结构里面的`tm_wday`属性和`tm_yday`属性，开发者自己不必填写这两个属性。所以，这个函数常用来获得指定时间是星期几（`tm_wday`）。

struct tm 结构的`tm_isdst`属性也可以设为`-1`，让`mktime()`决定是否应该采用夏令时。

下面是一个例子。

```c
struct tm some_time = {
  .tm_year=82,   // 距离 1900 的年数
  .tm_mon=3,     // 月份 [0, 11]
  .tm_mday=12,   // 天数 [1, 31]
  .tm_hour=12,   // 小时 [0, 23]
  .tm_min=00,    // 分钟 [0, 59]
  .tm_sec=04,    // 秒数 [0, 60]
  .tm_isdst=-1,  // 夏令时
};
    
time_t some_time_epoch;
some_time_epoch = mktime(&some_time);
    
// 输出 Mon Apr 12 12:00:04 1982
printf("%s", ctime(&some_time_epoch));

// 输出 Is DST: 0
printf("Is DST: %d\n", some_time.tm_isdst);
```

## difftime()

`difftime()`用来计算两个时间之间的差异。Unix 系统上，直接相减两个 time_t 值，就可以得到相差的秒数，但是为了程序的可移植性，最好还是使用这个函数。

```c
double difftime( time_t time1, time_t time2 );
```

`difftime()`函数接受两个 time_t 类型的时间作为参数，计算 time1 - time2 的差，并把结果转换为秒。

注意它的返回值是 double 类型。

```c
#include <stdio.h>
#include <time.h>
    
int main(void) {
  struct tm time_a = {
    .tm_year=82, 
    .tm_mon=3,   
    .tm_mday=12, 
    .tm_hour=4,  
    .tm_min=00,  
    .tm_sec=04,  
    .tm_isdst=-1,
  };
    
  struct tm time_b = {
    .tm_year=120,
    .tm_mon=10,  
    .tm_mday=15, 
    .tm_hour=16, 
    .tm_min=27,  
    .tm_sec=00,  
    .tm_isdst=-1,
  };
    
  time_t cal_a = mktime(&time_a);
  time_t cal_b = mktime(&time_b);
    
  double diff = difftime(cal_b, cal_a);
    
  double years = diff / 60 / 60 / 24 / 365.2425;
  
  // 输出 1217996816.000000 seconds (38.596783 years) between events
  printf("%f seconds (%f years) between events\n", diff, years);
}
```

上面示例中，折算年份时，为了尽量准确，使用了一年的准确长度 365.2425 天，这样可以抵消闰年的影响。

## strftime()

`strftime()`函数用来将 struct tm 结构转换为一个指定格式的字符串，并复制到指定地址。

```c
size_t strftime(
  char* str, 
  size_t maxsize, 
  const char* format, 
  const struct tm* timeptr
)
```

`strftime()`接受四个参数。

- 第一个参数：目标字符串的指针。
- 第二个参数：目标字符串可以接受的最大长度。
- 第三个参数：格式字符串。
- 第四个参数：struct tm 结构。

如果执行成功（转换并复制），`strftime()`函数返回复制的字符串长度；如果执行失败，返回`-1`。

下面是一个例子。

```c
#include <stdio.h>
#include <time.h>

int main(void) {
  char s[128];
  time_t now = time(NULL);

  // %c: 本地时间
  strftime(s, sizeof s, "%c", localtime(&now));
  puts(s);   // Sun Feb 28 22:29:00 2021

  // %A: 完整的星期日期的名称
  // %B: 完整的月份名称
  // %d: 月份的天数
  strftime(s, sizeof s, "%A, %B %d", localtime(&now));
  puts(s);   // Sunday, February 28

  // %I: 小时（12小时制）
  // %M: 分钟
  // %S: 秒数
  // %p: AM 或 PM
  strftime(s, sizeof s, "It's %I:%M:%S %p", localtime(&now));
  puts(s);   // It's 10:29:00 PM

  // %F: ISO 8601 yyyy-mm-dd 格式
  // %T: ISO 8601 hh:mm:ss 格式
  // %z: ISO 8601 时区
  strftime(s, sizeof s, "ISO 8601: %FT%T%z", localtime(&now));
  puts(s);   // ISO 8601: 2021-02-28T22:29:00-0800
}
```

下面是常用的格式占位符。

- %%：输出 % 字符。
- %a：星期几的简写形式，以当地时间计算。
- %A：星期几的完整形式，以当地时间计算。
- %b：月份的简写形式，以当地时间计算。
- %B：月份的完整形式，以当地时间计算。
- %c：日期和时间，使用“%x %X”。
- %d：月份的天数（01-31）。
- %H：小时，采用24小时制（00-23）。
- %I：小时，采用12小时制（00-12）。
- %J：一年的第几天（001-366）。
- %m：月数（01-12）。
- %M：分钟（00～59）。
- %P：AM 或 PM。
- %R：相当于"%H:%M"。
- %S：秒（00-61）。
- %U：一年的第几星期（00-53），以星期日为第1天。
- %w：一星期的第几天，星期日为第0天。
- %W：一年的第几星期(00-53)，以星期一为第1天。
- %x：完整的年月日的日期，以当地时间计算。
- %X：完整的时分秒的时间，以当地时间计算。
- %y：两位数年份（00-99）。
- %Y：四位数年份（例如 1984）。
- %Z：时区的简写。

## timespec_get()

`timespec_get()`用来将当前时间转成距离时间纪元的纳秒数（十亿分之一秒）。

```c
int timespec_get ( struct timespec* ts, int base ) ;
```

`timespec_get()`接受两个参数。

第一个参数是 struct timespec 结构指针，用来保存转换后的时间信息。struct timespec 的结构如下。

```c
struct timespec {
  time_t tv_sec;   // 秒数
  long   tv_nsec;  // 纳秒
};
```

第二个参数是一个整数，表示时间计算的起点。标准只给出了宏 TIME_UTC 这一个可能的值，表示返回距离时间纪元的秒数。

下面是一个例子。

```c
struct timespec ts;
    
timespec_get(&ts, TIME_UTC);
    
// 1614581530 s, 806325800 ns
printf("%ld s, %ld ns\n", ts.tv_sec, ts.tv_nsec);
    
double float_time = ts.tv_sec + ts.tv_nsec/1000000000.0;

// 1614581530.806326 seconds since epoch
printf("%f seconds since epoch\n", float_time);
```

## clock()

`clock()`函数返回从程序开始执行到当前的 CPU 时钟周期。一个时钟周期等于 CPU 频率的倒数，比如 CPU 的频率如果是 1G Hz，表示1秒内时钟信号可以变化 10^9 次，那么每个时钟周期就是 10^-9 秒。

```c
clock_t　clock(void);
```

`clock()`函数返回一个数字，表示从程序开始到现在的 CPU 时钟周期的次数。这个值的类型是 clock_t，一般是 long int 类型。 

为了把这个值转换为秒，应该把它除以常量`CLOCKS_PER_SEC`（每秒的时钟周期），这个常量也由`time.h`定义。

```c
printf("CPU time: %f\n", clock() / (double)CLOCKS_PER_SEC);
```

上面示例可以输出程序从开始到运行到这一行所花费的秒数。

如果计算机无法提供 CPU 时间，或者返回值太大，无法用`clock_t`类型表示，`clock()`函数就返回`-1`。

为了知道某个操作所耗费的精确时间，需要调用两次`clock()`，然后将两次的返回值相减。

```c
clock_t start = clock();

// ... 执行某些操作

clock_t end = clock();

long double seconds = (float)(end - start) / CLOCKS_PER_SEC;
```

## 参考链接

- [How to Measure Execution Time of a Program](https://serhack.me/articles/measure-execution-time-program/)
