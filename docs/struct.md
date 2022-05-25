# struct 结构

## 简介

C 语言内置的数据类型，除了最基本的几种原始类型，只有数组属于复合类型，可以同时包含多个值，但是只能包含相同类型的数据，实际使用中并不够用。

实际使用中，主要有下面两种情况，需要更灵活强大的复合类型。

- 复杂的物体需要使用多个变量描述，这些变量都是相关的，最好有某种机制将它们联系起来。
- 某些函数需要传入多个参数，如果一个个按照顺序传入，非常麻烦，最好能组合成一个复合结构传入。

为了解决这些问题，C 语言提供了`struct`关键字，允许自定义复合数据类型，将不同类型的值组合在一起。这样不仅为编程提供方便，也有利于增强代码的可读性。C 语言没有其他语言的对象（object）和类（class）的概念，struct 结构很大程度上提供了对象和类的功能。

下面是`struct`自定义数据类型的一个例子。

```c
struct fraction {
  int numerator;
  int denominator;
};
```

上面示例定义了一个分数的数据类型`struct fraction`，包含两个属性`numerator`和`denominator`。

注意，作为一个自定义的数据类型，它的类型名要包括`struct`关键字，比如上例是`struct fraction`，单独的`fraction`没有任何意义，甚至脚本还可以另外定义名为`fraction`的变量，虽然这样很容易造成混淆。另外，`struct`语句结尾的分号不能省略，否则很容易产生错误。

定义了新的数据类型以后，就可以声明该类型的变量，这与声明其他类型变量的写法是一样的。

```c
struct fraction f1;

f1.numerator = 22;
f1.denominator = 7;
```

上面示例中，先声明了一个`struct fraction`类型的变量`f1`，这时编译器就会为`f1`分配内存，接着就可以为`f1`的不同属性赋值。可以看到，struct 结构的属性通过点（`.`）来表示，比如`numerator`属性要写成`f1.numerator`。

再提醒一下，声明自定义类型的变量时，类型名前面，不要忘记加上`struct`关键字。也就是说，必须使用`struct fraction f1`声明变量，不能写成`fraction f1`。

除了逐一对属性赋值，也可以使用大括号，一次性对 struct 结构的所有属性赋值。

```c
struct car {
  char* name;
  float price;
  int speed;
};

struct car saturn = {"Saturn SL/2", 16000.99, 175};
```

上面示例中，变量`saturn`是`struct car`类型，大括号里面同时对它的三个属性赋值。如果大括号里面的值的数量，少于属性的数量，那么缺失的属性自动初始化为`0`。

注意，大括号里面的值的顺序，必须与 struct 类型声明时属性的顺序一致。否则，必须为每个值指定属性名。

```c
struct car saturn = {.speed=172, .name="Saturn SL/2"};
```

上面示例中，初始化的属性少于声明时的属性，这时剩下的那些属性都会初始化为`0`。

声明变量以后，可以修改某个属性的值。

```c
struct car saturn = {.speed=172, .name="Saturn SL/2"};
saturn.speed = 168;
```

上面示例将`speed`属性的值改成`168`。

struct 的数据类型声明语句与变量的声明语句，可以合并为一个语句。

```c
struct book {
  char title[500];
  char author[100];
  float value;
} b1;
```

上面的语句同时声明了数据类型`book`和该类型的变量`b1`。如果类型标识符`book`只用在这一个地方，后面不再用到，这里可以将类型名省略。

```c
struct {
  char title[500];
  char author[100];
  float value;
} b1;
```

上面示例中，`struct`声明了一个匿名数据类型，然后又声明了这个类型的变量`b1`。

与其他变量声明语句一样，可以在声明变量的同时，对变量赋值。

```c
struct {
  char title[500];
  char author[100];
  float value;
} b1 = {"Harry Potter", "J. K. Rowling", 10.0},
  b2 = {"Cancer Ward", "Aleksandr Solzhenitsyn", 7.85};
```

上面示例中，在声明变量`b1`和`b2`的同时，为它们赋值。

下一章介绍的`typedef`命令可以为 struct 结构指定一个别名，这样使用起来更简洁。

```c
typedef struct cell_phone {
  int cell_no;
  float minutes_of_charge;
} phone;

phone p = {5551234, 5};
```

上面示例中，`phone`就是`struct cell_phone`的别名。

指针变量也可以指向`struct`结构。

```c
struct book {
  char title[500];
  char author[100];
  float value;
}* b1;

// 或者写成两个语句
struct book {
  char title[500];
  char author[100];
  float value;
};
struct book* b1;
```

上面示例中，变量`b1`是一个指针，指向的数据是`struct book`类型的实例。

struct 结构也可以作为数组成员。

```c
struct fraction numbers[1000];

numbers[0].numerator = 22;
numbers[0].denominator = 7;
```

上面示例声明了一个有1000个成员的数组`numbers`，每个成员都是自定义类型`fraction`的实例。

struct 结构占用的存储空间，不是各个属性存储空间的总和，而是最大内存占用属性的存储空间的倍数，其他属性会添加空位与之对齐。这样可以提高读写效率。

```c
struct foo {
  int a;
  char* b;
  char c;
};
printf("%d\n", sizeof(struct foo)); // 24
```

上面示例中，`struct foo`有三个属性，在64位计算机上占用的存储空间分别是：`int a`占4个字节，指针`char* b`占8个字节，`char c`占1个字节。它们加起来，一共是13个字节（4 + 8 + 1）。但是实际上，`struct foo`会占用24个字节，原因是它最大的内存占用属性是`char* b`的8个字节，导致其他属性的存储空间也是8个字节，这样才可以对齐，导致整个`struct foo`就是24个字节（8 * 3）。

多出来的存储空间，都采用空位填充，所以上面的`struct
foo`真实的结构其实是下面这样。

```c
struct foo {
  int a;        // 4
  char pad1[4]; // 填充4字节
  char *b;      // 8
  char c;       // 1
  char pad2[7]; // 填充7字节
};
printf("%d\n", sizeof(struct foo)); // 24
```

为什么浪费这么多空间进行内存对齐呢？这是为了加快读写速度，把内存占用划分成等长的区块，就可以快速在 Struct 结构体中定位到每个属性的起始地址。

由于这个特性，在有必要的情况下，定义 Struct 结构体时，可以采用存储空间递增的顺序，定义每个属性，这样就能节省一些空间。

```c
struct foo {
  char c;
  int a;
  char* b;
};
printf("%d\n", sizeof(struct foo)); // 16
```

上面示例中，占用空间最小的`char c`排在第一位，其次是`int a`，占用空间最大的`char* b`排在最后。整个`strct foo`的内存占用就从24字节下降到16字节。

## struct 的复制

struct 变量可以使用赋值运算符（`=`），复制给另一个变量，这时会生成一个全新的副本。系统会分配一块新的内存空间，大小与原来的变量相同，把每个属性都复制过去，即原样生成了一份数据。这一点跟数组的复制不一样，务必小心。

```c
struct cat { char name[30]; short age; } a, b;

strcpy(a.name, "Hula");
a.age = 3;

b = a;
b.name[0] = 'M';

printf("%s\n", a.name); // Hula
printf("%s\n", b.name); // Mula
```

上面示例中，变量`b`是变量`a`的副本，两个变量的值是各自独立的，修改掉`b.name`不影响`a.name`。

上面这个示例是有前提的，就是 struct 结构的属性必须定义成字符数组，才能复制数据。如果稍作修改，属性定义成字符指针，结果就不一样。

```c
struct cat { char* name; short age; } a, b;

a.name = "Hula";
a.age = 3;

b = a;
```

上面示例中，`name`属性变成了一个字符指针，这时`a`赋值给`b`，导致`b.name`也是同样的字符指针，指向同一个地址，也就是说两个属性共享同一个地址。因为这时，struct 结构内部保存的是一个指针，而不是上一个例子的数组，这时复制的就不是字符串本身，而是它的指针。并且，这个时候也没法修改字符串，因为字符指针指向的字符串是不能修改的。

总结一下，赋值运算符（`=`）可以将 struct 结构每个属性的值，一模一样复制一份，拷贝给另一个 struct 变量。这一点跟数组完全不同，使用赋值运算符复制数组，不会复制数据，只会共享地址。

注意，这种赋值要求两个变量是同一个类型，不同类型的 struct 变量无法互相赋值。

另外，C 语言没有提供比较两个自定义数据结构是否相等的方法，无法用比较运算符（比如`==`和`!=`）比较两个数据结构是否相等或不等。

## struct 指针

如果将 struct 变量传入函数，函数内部得到的是一个原始值的副本。

```c
#include <stdio.h>

struct turtle {
  char* name;
  char* species;
  int age;
};

void happy(struct turtle t) {
  t.age = t.age + 1;
}

int main() {
  struct turtle myTurtle = {"MyTurtle", "sea turtle", 99};
  happy(myTurtle);
  printf("Age is %i\n", myTurtle.age); // 输出 99
  return 0;
}
```

上面示例中，函数`happy()`传入的是一个 struct 变量`myTurtle`，函数内部有一个自增操作。但是，执行完`happy()`以后，函数外部的`age`属性值根本没变。原因就是函数内部得到的是 struct 变量的副本，改变副本影响不到函数外部的原始数据。

通常情况下，开发者希望传入函数的是同一份数据，函数内部修改数据以后，会反映在函数外部。而且，传入的是同一份数据，也有利于提高程序性能。这时就需要将 struct 变量的指针传入函数，通过指针来修改 struct 属性，就可以影响到函数外部。

struct 指针传入函数的写法如下。

```c
void happy(struct turtle* t) {
}

happy(&myTurtle);
```

上面代码中，`t`是 struct 结构的指针，调用函数时传入的是指针。struct 类型跟数组不一样，类型标识符本身并不是指针，所以传入时，指针必须写成`&myTurtle`。

函数内部也必须使用`(*t).age`的写法，从指针拿到 struct 结构本身。

```c
void happy(struct turtle* t) {
  (*t).age = (*t).age + 1;
}
```

上面示例中，`(*t).age`不能写成`*t.age`，因为点运算符`.`的优先级高于`*`。`*t.age`这种写法会将`t.age`看成一个指针，然后取它对应的值，会出现无法预料的结果。

现在，重新编译执行上面的整个示例，`happy()`内部对 struct 结构的操作，就会反映到函数外部。

`(*t).age`这样的写法很麻烦。C 语言就引入了一个新的箭头运算符（`->`），可以从 struct 指针上直接获取属性，大大增强了代码的可读性。

```c
void happy(struct turtle* t) {
  t->age = t->age + 1;
}
```

总结一下，对于 struct 变量名，使用点运算符（`.`）获取属性；对于 struct 变量指针，使用箭头运算符（`->`）获取属性。以变量`myStruct`为例，假设`ptr`是它的指针，那么下面三种写法是同一回事。

```c
// ptr == &myStruct
myStruct.prop == (*ptr).prop == ptr->prop
```

## struct 的嵌套

struct 结构的成员可以是另一个 struct 结构。

```c
struct species {
  char* name;
  int kinds;
};

struct fish {
  char* name;
  int age;
  struct species breed;
};
```

上面示例中，`fish`的属性`breed`是另一个 struct 结构`species`。

赋值的时候有多种写法。

```c
// 写法一
struct fish shark = {"shark", 9, {"Selachimorpha", 500}};

// 写法二
struct species myBreed = {"Selachimorpha", 500};
struct fish shark = {"shark", 9, myBreed};

// 写法三
struct fish shark = {
  .name="shark",
  .age=9,
  .breed={"Selachimorpha", 500}
};

// 写法四
struct fish shark = {
  .name="shark",
  .age=9,
  .breed.name="Selachimorpha",
  .breed.kinds=500
};

printf("Shark's species is %s", shark.breed.name);
```

上面示例展示了嵌套 Struct 结构的四种赋值写法。另外，引用`breed`属性的内部属性，要使用两次点运算符（`shark.breed.name`）。

下面是另一个嵌套 struct 的例子。

```c
struct name {
  char first[50];
  char last[50];
};

struct student {
  struct name name;
  short age;
  char sex;
} student1;

strcpy(student1.name.first, "Harry");
strcpy(student1.name.last, "Potter");

// or
struct name myname = {"Harry", "Potter"};
student1.name = myname;
```

上面示例中，自定义类型`student`的`name`属性是另一个自定义类型，如果要引用后者的属性，就必须使用两个`.`运算符，比如`student1.name.first`。另外，对字符数组属性赋值，要使用`strcpy()`函数，不能直接赋值，因为直接改掉字符数组名的地址会报错。

struct 结构内部不仅可以引用其他结构，还可以自我引用，即结构内部引用当前结构。比如，链表结构的节点就可以写成下面这样。

```c
struct node {
  int data;
  struct node* next;
};
```

上面示例中，`node`结构的`next`属性，就是指向另一个`node`实例的指针。下面，使用这个结构自定义一个数据链表。

```c
struct node {
  int data;
  struct node* next;
};

struct node* head;

// 生成一个三个节点的列表 (11)->(22)->(33)
head = malloc(sizeof(struct node));

head->data = 11;
head->next = malloc(sizeof(struct node));

head->next->data = 22;
head->next->next = malloc(sizeof(struct node));

head->next->next->data = 33;
head->next->next->next = NULL;

// 遍历这个列表
for (struct node *cur = head; cur != NULL; cur = cur->next) {
  printf("%d\n", cur->data);
}
```

上面示例是链表结构的最简单实现，通过`for`循环可以对其进行遍历。

## 位字段

struct 还可以用来定义二进制位组成的数据结构，称为“位字段”（bit field），这对于操作底层的二进制数据非常有用。

```c
struct {
  unsigned int ab:1;
  unsigned int cd:1;
  unsigned int ef:1;
  unsigned int gh:1;
} synth;

synth.ab = 0;
synth.cd = 1;
```

上面示例中，每个属性后面的`:1`，表示指定这些属性只占用一个二进制位，所以这个数据结构一共是4个二进制位。

注意，定义二进制位时，结构内部的各个属性只能是整数类型。

实际存储的时候，C 语言会按照`int`类型占用的字节数，存储一个位字段结构。如果有剩余的二进制位，可以使用未命名属性，填满那些位。也可以使用宽度为0的属性，表示占满当前字节剩余的二进制位，迫使下一个属性存储在下一个字节。

```c
struct {
  unsigned int field1 : 1;
  unsigned int        : 2;
  unsigned int field2 : 1;
  unsigned int        : 0;
  unsigned int field3 : 1;
} stuff;
```

上面示例中，`stuff.field1`与`stuff.field2`之间，有一个宽度为两个二进制位的未命名属性。`stuff.field3`将存储在下一个字节。

## 弹性数组成员

很多时候，不能事先确定数组到底有多少个成员。如果声明数组的时候，事先给出一个很大的成员数，就会很浪费空间。C 语言提供了一个解决方法，叫做弹性数组成员（flexible array member）。

如果不能事先确定数组成员的数量时，可以定义一个 struct 结构。

```c
struct vstring {
  int len;
  char chars[];
};
```

上面示例中，`struct vstring`结构有两个属性。`len`属性用来记录数组`chars`的长度，`chars`属性是一个数组，但是没有给出成员数量。

`chars`数组到底有多少个成员，可以在为`vstring`分配内存时确定。

```c
struct vstring* str = malloc(sizeof(struct vstring) + n * sizeof(char));
str->len = n;
```

上面示例中，假定`chars`数组的成员数量是`n`，只有在运行时才能知道`n`到底是多少。然后，就为`struct vstring`分配它需要的内存：它本身占用的内存长度，再加上`n`个数组成员占用的内存长度。最后，`len`属性记录一下`n`是多少。

这样就可以让数组`chars`有`n`个成员，不用事先确定，可以跟运行时的需要保持一致。

弹性数组成员有一些专门的规则。首先，弹性成员的数组，必须是 struct 结构的最后一个属性。另外，除了弹性数组成员，struct 结构必须至少还有一个其他属性。

