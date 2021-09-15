# errno.h

## errno 变量

`errno.h`声明了一个 int 类型的 errno 变量，用来存储错误码（正整数）。

如果这个变量有非零值，表示已经执行的程序发生了错误。

```c
int x = -1;

errno = 0;

int y = sqrt(x);

if (errno != 0) {
  fprintf(stderr, "sqrt error; program terminated.\n");
  exit(EXIT_FAILURE);
}
```

上面示例中，计算一个负值的平方根是不允许的，会导致`errno`不等于`0`。

如果要检查某个函数是否发生错误，必须在即将调用该函数之前，将`errno`的值置为0，防止其他函数改变`errno`的值。

## 宏

变量`errno`的值通常是两个宏`EDOM`或`ERANGE`。这两个宏都定义在`errno.h`。它们表示调用数学函数时，可能发生的两种错误。

- 定义域错误（EDOM）：传递给函数的一个参数超出了函数的定义域。例如，负数传入`sqrt()`作为参数。
- 取值范围错误（ERANGE）：函数的返回值太大，无法用返回类型表示。例如，1000 传入`exp()`作为参数，因为 e^1000 太大，无法使用 double 类型表示。

使用数学函数时，可以将`errno`的值与 EDOM 和 ERANGE 比较，用来确定到底发生了哪一类错误。

