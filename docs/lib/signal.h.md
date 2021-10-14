# signal.h

## 简介

`signal.h`提供了信号（即异常情况）的处理工具。所谓“信号”（signal），可以理解成系统与程序之间的短消息，主要用来表示运行时错误，或者发生了异常事件。

头文件`signal.h`定义了一系列宏，表示不同的信号。

- SIGABRT：异常中止（可能由于调用了 abort() 方法）。
- SIGFPE：算术运算发生了错误（可能是除以 0 或者溢出）。
- SIGILL：无效指令。
- SIGINT：中断。
- SIGSEGV：无效内存访问。
- SIGTERM：终止请求。

上面每个宏的值都是一个正整数常量。

## signal()

头文件`signal.h`还定义了一个`signal()`函数，用来指定某种信号的处理函数。

```c
signal(SIGINT, handler);
```

`signal()`接受两个参数，第一个参数是某种信号的宏，第二个参数是处理这个信号的函数指针`handler`。

信号处理函数`handler`接受一个 int 类型的参数，表示信号类型。它的原型如下。

```c
void (*func)(int);
```

`handler`函数体内部可以根据这个整数，判断到底接受到了哪种信号，因为多个信号可以共用同一个处理函数。一旦处理函数执行完成，程序会从信号发生点恢复执行。但是，如果遇到 SIGABRT 信号，处理函数执行完成，系统会让程序中止。

当系统向程序发送信号时，程序可以忽略信号，即不指定处理函数。

`signal()`的返回值是前一个处理函数的指针，常常把它保存在变量之中，当新的处理函数执行完，再恢复以前的处理函数。

```c
void (*orig_handler)(int);
orig_handler = signal(SIGINT, handler);
// SIGINT 信号发生之后
signal(SIGINT, orig_handler);
```

上面示例中，`signal()`为信号`SIGINT`指定了新的处理函数`handler`，把原来的处理函数保存在变量`orig_handler`里面。等到`handler`这个函数用过之后，再恢复原来的处理函数。

## 信号相关的宏

`signal.h`还提供了信号相关的宏。

（1）SIG_DFL

SIG_DFL 表示默认的处理函数。

```c
signal(SIGINT, SIG_DFL);
```

上面示例中，SIGINT 的处理函数是默认处理函数，由当前实现决定。

（2）SIG_IGN

SIG_IGN 表示忽略该信号。

```c
signal(SIGINT, SIG_IGN);
```

上面示例表示不对 SIGINT 信号进行处理。由于程序运行时按下 Ctrl + c 是发出 SIGINT 信号，所以使用该语句后，程序无法用 Ctrl + c 终止。

（3）SIG_ERR

SIG_ERR 是信号处理函数发生错误时，`signal()`的返回值。

```c
if (signal(SIGINT, handler) == SIG_ERR) {
  perror("signal(SIGINT, handler) failed");
  // ...
}
```

上面示例可以判断`handler`处理 SIGINT 时，是否发生错误。

## raise()

`raise()`函数用来在程序中发出信号。

```c
int raise(int sig);
```

它接受一个信号值作为参数，表示发出该信号。它的返回值是一个整数，可以用来判断信号发出是否成功，0 表示成功，非 0 表示失败。

```c
void handler(int sig) {
  printf("Handler called for signal %d\n", sig);
}

signal(SIGINT, handler);
raise(SIGINT);
```

上面示例中，`raise()`触发 SIGINT 信号，导致 handler 函数执行。
