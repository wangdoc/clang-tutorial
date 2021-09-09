# 流程控制

C 语言的程序是顺序执行，即先执行前面的语句，再执行后面的语句。开发者如果想要控制程序执行的流程，就必须使用流程控制的语法结构，主要是条件执行和循环执行。

## if 语句

`if`语句用于条件判断，满足条件时，就执行指定的语句。

```c
if (expression) statement
```

上面式子中，表达式`expression`为真（值不为`0`）时，就执行`statement`语句。

`if`后面的判断条件`expression`外面必须有圆括号，否则会报错。语句体部分`statement`可以是一个语句，也可以是放在大括号里面的复合语句。下面是一个例子。

```c
if (x == 10) printf("x is 10");
```

上面示例中，当变量`x`为`10`时，就会输出一行文字。对于只有一个语句的语句体，语句部分通常另起一行。

```c
if (x == 10)
  printf("x is 10\n");
```

如果有多条语句，就需要把它们放在大括号里面，组成一个复合语句。

```c
if (line_num == MAX_LINES) {
  line_num = 0;
  page_num++;
}
```

`if`语句可以带有`else`分支，指定条件不成立时（表达式`expression`的值为`0`），所要执行的代码。

```c
if (expression) statement
else statement
```

下面是一个例子。

```c
if (i > j)
  max = i;
else
  max = j;
```

如果`else`的语句部分多于一行，同样可以把它们放在大括号里面。

`else`可以与另一个`if`语句连用，构成多重判断。

```c
if (expression)
  statement
else if (expression)
  statement
...
else if (expression)
  statement
else
  statement
```

如果有多个`if`和`else`，可以记住这样一条规则，`else`总是跟最接近的`if`匹配。

```c
if (number > 6)
  if (number < 12)
    printf("The number is more than 6, less than 12.\n");
else
  printf("It is wrong number.\n");
```

上面示例中，`else`部分匹配最近的`if`（即`number < 12`），所以如果`number`等于6，就不会执行`else`的部分。

这样很容易出错，为了提供代码的可读性，建议使用大括号，明确`else`匹配哪一个`if`。

```c
if (number > 6) {
  if (number < 12) {
    printf("The number is more than 6, less than 12.\n");
  }
} else {
  printf("It is wrong number.\n");
}
```

上面示例中，使用了大括号，就可以清晰地看出`else`匹配外层的`if`。

## 三元运算符 ?:

C 语言有一个三元表达式`?:`，可以用作`if...else`的简写形式。

```c
<expression1> ? <expression2> : <expression3>
```

这个操作符的含义是，表达式`expression1`如果为`true`（非0值），就执行`expression2`，否则执行`expression3`。

下面是一个例子，返回两个值之中的较大值。

```c
(i > j) ? i : j;
```

上面的代码等同于下面的`if`语句。

```c
if (i > j)
  return i;
else
  return j;
```

## switch 语句

switch 语句是一种特殊形式的 if...else 结构，用于判断条件有多个结果的情况。它把多重的`else if`改成更易用、可读性更好的形式。

```c
switch (expression) {
  case value1: statement
  case value2: statement
  default: statement
}
```

上面代码中，根据表达式`expression`不同的值，执行相应的`case`分支。如果找不到对应的值，就执行`default`分支。

下面是一个例子。

```c
switch (grade) {
  case 0:
    printf("False");
    break;
  case 1:
    printf("True");
    break;
  default:
    printf("Illegal");
}
```

上面示例中，根据变量`grade`不同的值，会执行不同的`case`分支。如果等于`0`，执行`case 0`的部分；如果等于`1`，执行`case 1`的部分；否则，执行`default`的部分。`default`表示处理以上所有`case`都不匹配的情况。

每个`case`语句体的结尾，都应该有一个`break`语句，作用是跳出整个`switch`结构，不再往下执行。如果缺少`break`，就会导致继续执行下一个`case`或`default`分支。

```c
switch (grade) {
  case 0:
    printf("False");
  case 1:
    printf("True");
    break;
  default:
    printf("Illegal");
}
```

上面示例中，`case 0`的部分没有`break`语句，导致这个分支执行完以后，不会跳出`switch`结构，继续执行`case 1`分支。

利用这个特点，如果多个`case`分支对应同样的语句体，可以写成下面这样。

```c
switch (grade) {
  case 0:
  case 1:
    printf("True");
    break;
  default:
    printf("Illegal");
}
```

上面示例中，`case 0`分支没有任何语句，导致`case 0`和`case 1`都会执行同样的语句体。

`case`后面的语句体，不用放在大括号里面，这也是为什么需要`break`的原因。

`default`分支用来处理前面的 case 都不匹配的情况，最好放在所有 case 的后面，这样就不用写`break`语句。这个分支是可选的，如果没有该分支，遇到所有的 case 都不匹配的情况，就会直接跳出整个 switch 代码块。

## while 语句

`while`语句用于循环结构，满足条件时，不断执行循环体。

```c
while (expression)
  statement
```

上面代码中，如果表达式`expression`为非零值（表示真），就会执行`statement`语句，然后再次判断`expression`是否为零；如果`expression`为零（表示伪）就跳出循环，不再执行循环体。

```c
while (i < n)
  i = i + 2;
```

上面示例中，只要`i`小于`n`，`i`就会不断增加2。

如果循环体有多个语句，就需要使用大括号将这些语句组合在一起。

```c
while (expression) {
  statement;
  statement;
}
```

下面是一个例子。

```c
i = 0;

while (i < 10) {
  printf("i is now %d!\n", i);
  i++;
}

printf("All done!\n");
```

上面代码中，循环体会执行10次，每次将`i`增加`1`，直到等于`10`才退出循环。

只要条件为真，`while`会产生无限循环。下面是一种常见的无限循环的写法。

```c
while (1) {
  // ...
}
```

上面的示例虽然是无限循环，但是循环体内部可以用`break`语句跳出循环。

## do...while 结构

`do...while`结构是`while`的变体，它会先执行一次循环体，然后再判断是否满足条件。如果满足的话，就继续执行循环体，否则跳出循环。

```c
do statement
while (expression);
```

上面代码中，不管条件`expression`是否成立，循环体`statement`至少会执行一次。每次`statement`执行完毕，就会判断一次`expression`，决定是否结束循环。

```c
i = 10;

do --i;
while (i > 0);
```

上面示例中，变量`i`先减去1，再判断是否大于0。如果大于0，就继续减去1，直到`i`等于`0`为止。

如果循环部分有多条语句，就需要放在大括号里面。

```c
i = 10;

do {
  printf("i is %d\n", i);
  i++;
} while (i < 10);

printf("All done!\n");
```

上面例子中，变量`i`并不满足小于`10`的条件，但是循环体还是会执行一次。

## for 语句

`for`语句是最常用的循环结构，通常用于精确控制循环次数。

```c
for (initialization; continuation; action)
  statement;
```

上面代码中，`for`语句的条件部分（即圆括号里面的部分）有三个表达式。

- `initialization`：初始化表达式，用于初始化循环变量，只执行一次。
- `continuation`：判断表达式，只要为`true`，就会不断执行循环体。
- `action`：循环变量处理表达式，每轮循环结束后执行，使得循环变量发生变化。

循环体部分的`statement`可以是一条语句，也可以是放在大括号里面的复合语句。下面是一个例子。

```c
for (int i = 10; i > 0; i--)
  printf("i is %d\n", i);
```

上面示例中，循环变量`i`在`for`的第一个表达式里面声明，该变量只用于本次循环。离开循环体之后，就会失效。

条件部分的三个表达式，每一个都可以有多个语句，语句与语句之间使用逗号分隔。

```c
int i, j;
for (i = 0, j = 999; i < 10; i++, j--) {
  printf("%d, %d\n", i, j);
}
```

上面示例中，初始化部分有两个语句，分别对变量`i`和`j`进行赋值。

`for`的三个表达式都不是必需的，甚至可以全部省略，这会形成无限循环。

```c
for (;;) {
  printf("本行会无限循环地打印。\n" );
}
```

上面示例由于没有判断条件，就会形成无限循环。

## break 语句

`break`语句有两种用法。一种是与`switch`语句配套使用，用来中断某个分支的执行，这种用法前面已经介绍过了。另一种用法是在循环体内部跳出循环，不再进行后面的循环了。

```c
for (int i = 0; i < 3; i++) {
  for (int j = 0; j < 3; j++) {
    printf("%d, %d\n", i, j);
    break;
  }
}
```

上面示例中，`break`语句使得循环跳到下一个`i`。

```c
while ((ch = getchar()) != EOF) {
  if (ch == '\n') break;
  putchar(ch);
}
```

上面示例中，一旦读到换行符（`\n`），`break`命令就跳出整个`while`循环，不再继续读取了。

注意，`break`命令只能跳出循环体和`switch`结构，不能跳出`if`结构。

```c
if (n > 1) {
  if (n > 2) break; // 无效
  printf("hello\n");
}
```

上面示例中，`break`语句是无效的，因为它不能跳出外层的`if`结构。

## continue 语句

`continue`语句用于在循环体内部终止本轮循环，进入下一轮循环。只要遇到`continue`语句，循环体内部后面的语句就不执行了，回到循环体的头部，开始执行下一轮循环。

```c
for (int i = 0; i < 3; i++) {
  for (int j = 0; j < 3; j++) {
    printf("%d, %d\n", i, j);
    continue;
  }
}
```

上面示例中，有没有`continue`语句，效果一样，都表示跳到下一个`j`。

```c
while ((ch = getchar()) != '\n') {
  if (ch == '\t') continue;
  putchar(ch);
}
```

上面示例中，只要读到的字符是制表符（`\t`），就用`continue`语句跳过该字符，读取下一个字符。

## goto 语句

goto 语句用于跳到指定的标签名。这会破坏结构化编程，建议不要轻易使用，这里为了语法的完整，介绍一下它的用法。

```c
char ch;

top: ch = getchar();

if (ch == 'q')
  goto top;
```

上面示例中，`top`是一个标签名，可以放在正常语句的前面，相当于为这行语句做了一个标记。程序执行到`goto`语句，就会跳转到它指定的标签名。

```c
infinite_loop:
  print("Hello, world!\n");
  goto infinite_loop;
```

上面的代码会产生无限循环。

goto 的一个主要用法是跳出多层循环。

```c
for(...) {
  for (...) {
    while (...) {
      do {
        if (some_error_condition)
          goto bail;    
      } while(...);
    }
  }
}
    
bail:
// ... ...
```

上面代码有很复杂的嵌套循环，不使用 goto 的话，想要完全跳出所有循环，写起来很麻烦。

goto 的另一个用途是提早结束多重判断。

```c
if (do_something() == ERR)
  goto error;
if (do_something2() == ERR)
  goto error;
if (do_something3() == ERR)
  goto error;
if (do_something4() == ERR)
  goto error;
```

上面示例有四个判断，只要有一个发现错误，就使用 goto 跳过后面的判断。

注意，goto 只能在同一个函数之中跳转，并不能跳转到其他函数。

