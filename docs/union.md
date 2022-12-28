# Union 结构

有时需要一种数据结构，不同的场合表示不同的数据类型。比如，如果只用一种数据结构表示水果的“量”，这种结构就需要有时是整数（6个苹果），有时是浮点数（1.5公斤草莓）。

C 语言提供了 Union 结构，用来自定义可以灵活变更的数据结构。它内部包含各种属性，但是所有属性共用一块内存，导致这些属性都是对同一个二进制数据的解读，其中往往只有一个属性的解读是有意义的。并且，后面写入的属性会覆盖前面的属性，这意味着同一块内存，可以先供某一个属性使用，然后再供另一个属性使用。这样做的最大好处是节省内存空间。

```c
union quantity {
  short count;
  float weight;
  float volume;
};
```

上面示例中，`union`命令定义了一个包含三个属性的数据类型`quantity`。虽然包含三个属性，但是只能写入一个值，三个属性都是对这个值的不同解读。最后赋值的属性，往往就是可以取到有意义的值的那个属性。

使用时，声明一个该类型的变量。

```c
// 写法一
union quantity q;
q.count = 4;

// 写法二
union quantity q = {.count=4};

// 写法三
union quantity q = {4};
```

上面代码展示了为 Union 结构赋值的三种写法。最后一种写法不指定属性名，就会赋值给第一个属性。

执行完上面的代码以后，`q.count`可以取到值，另外两个属性取不到值。

```c
printf("count is %i\n", q.count); // count is 4
printf("weight is %f\n", q.weight); // 未定义行为
```

如果要让`q.weight`属性可以取到值，就要先为它赋值。

```c
q.weight = 0.5;
printf("weight is %f\n", q.weight); // weight is 0.5
```

一旦为其他属性赋值，原先可以取到值的`q.count`属性就跟着改变，使用它可能就没有意义了。除了这一点，Union 结构的其他用法与 Struct 结构，基本上是一致的。

Union 结构也支持指针运算符`->`。

```c
union quantity {
  short count;
  float weight;
  float volume;
};

union quantity q;
q.count = 4;

union quantity* ptr;
ptr = &q;

printf("%d\n", ptr->count); // 4
```

上面示例中，`ptr`是`q`的指针，那么`ptr->count`等同于`q.count`。

Union 结构指针与它的属性有关，当前正在按照哪个属性解读数据，它的指针就是对应的数据类型。

```c
union foo {
  int a;
  float b;
} x;

int* foo_int_p = (int *)&x;
float* foo_float_p = (float *)&x;

x.a = 12;
printf("%d\n", x.a);           // 12
printf("%d\n", *foo_int_p);    // 12

x.b = 3.141592;
printf("%f\n", x.b);           // 3.141592
printf("%f\n", *foo_float_p);  // 3.141592
```

上面示例中，`&x`是 foo 结构的指针，它的数据类型完全由当前赋值的属性决定。

typedef 命令可以为 Union 数据类型起别名。

```c
typedef union {
  short count;
  float weight;
  float volume;
} quantity;
```

上面示例中，`union`命令定义了一个包含三个属性的数据类型，`typedef`命令为它起别名为`quantity`。

Union 结构的好处，主要是节省空间。它将一段内存空间，重用于不同类型的数据。定义了三个属性，但同一时间只用到一个，使用 Union 结构就可以节省另外两个属性的空间。Union 结构占用的内存长度，等于它内部最长属性的长度。

