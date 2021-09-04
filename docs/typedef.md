# typedef 命令

## 简介

`typedef`命令用来为某个类型起别名。

```c
typedef type name;
```

上面代码中，`type`代表类型名，`name`代表别名。

```c
typedef unsigned char BYTE;

BYTE c = 'z';
```

上面示例中，`typedef`命令为类型`unsign char`起别名`BYTE`，然后就可以使用`BYTE`声明变量。

typedef 可以一次指定多个别名。

```c
typedef int antelope, bagel, mushroom;
```

上面示例中，一次性为`int`类型起了三个别名。

typedef 可以为指针起别名。

```c
typedef int* intptr;

int a = 10;
intptr x = &a;
```

上面示例中，`intptr`是`int*`的别名。不过，使用的时候要小心，这样不容易看出来，变量`x`是一个指针类型。

typedef 也可以用来为数组类型起别名。

```c
typedef int five_ints[5];

five_ints x = {11, 22, 33, 44, 55};
```

上面示例中，`five_ints`是一个数组类型，包含5个整数的

typedef 为函数起别名的写法如下。

```c
typedef signed char (*fp)(void);
```

上面示例中，类型别名`fp`是一个指针，代表函数`signed char (*)(void)`。

## 主要好处

`typedef`为类型起别名的好处，主要有下面几点。

（1）更好的代码可读性。

```c
typedef char* STRING;

STRING name;
```

上面示例为字符指针起别名为`STRING`，以后使用`STRING`声明变量时，就可以轻易辨别该变量是字符串。

（2）为 struct、union、enum 等命令定义的复杂数据结构创建别名，从而便于引用。

```c
struct treenode {
  // ...
};

typedef struct treenode* Tree;
```

上面示例中，`Tree`为`struct treenode*`的别名。

typedef 也可以与 struct 定义数据类型的命令写在一起。

```c
typedef struct animal {
  char* name;
  int leg_count, speed;
} animal;
```

上面示例中，自定义数据类型时，同时使用`typedef`命令，为`struct animal`起了一个别名`animal`。

这种情况下，C 语言允许省略 struct 命令后面的类型名。

```c
typedef struct {
  char *name;
  int leg_count, speed;
} animal;
```

上面示例相当于为一个匿名的数据类型起了别名`animal`。

（3）typedef 方便以后为变量改类型。

```c
typedef float app_float;

app_float f1, f2, f3;
```

上面示例中，变量`f1`、`f2`、`f3`的类型都是`float`。如果以后需要为它们改类型，只需要修改`typedef`语句即可。

```c
typedef long double app_float;
```

上面命令将变量`f1`、`f2`、`f3`的类型都改为`long double`。

（4）可移植性

某一个值在不同计算机上的类型，可能是不一样的。

```c
int i = 100000;
```

上面代码在32位整数的计算机没有问题，但是在16位整数的计算机就会出错。

C 语言的解决办法，就是提供了类型别名，在不同计算机上会解释成不同类型，比如`int32_t`。

```c
int32_t i = 100000;
```

上面示例将变量`i`声明成`int32_t`类型，保证它在不同计算机上都是32位宽度，移植代码时就不会出错。

这一类的类型别名都是用 typedef 定义的。下面是类似的例子。

```c
typedef long int ptrdiff_t;
typedef unsigned long int size_t;
typedef int wchar_t;
```

这些整数类型别名都放在头文件`stdint.h`，不同架构的计算机只需修改这个头文件即可，而无需修改代码。

因此，`typedef`有助于提高代码的可移植性，使其能适配不同架构的计算机。

（5）简化类型声明

C 语言有些类型声明相当复杂，比如下面这个。

```c
char (*(*x(void))[5])(void);
```

typedef 可以简化复杂的类型声明，使其更容易理解。首先，最外面一层起一个类型别名。

```c
typedef char (*Func)(void);
Func (*x(void))[5];
```

这个看起来还是有点复杂，就为里面一层也定义一个别名。

```c
typedef char (*Func)(void);
typedef Func Arr[5];
Arr* x(void);
```

上面代码就比较容易解读了。

- `x`是一个函数，返回一个指向 Arr 类型的指针。
- `Arr`是一个数组，有5个成员，每个成员是`Func`类型。
- `Func`是一个函数指针，指向一个无参数、返回字符值的函数。

