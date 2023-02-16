# 文件操作

本章介绍 C 语言如何操作文件。

## 文件指针

C 语言提供了一个 FILE 数据结构，记录了操作一个文件所需要的信息。该结构定义在头文件`stdio.h`，所有文件操作函数都要通过这个数据结构，获取文件信息。

开始操作一个文件之前，就要定义一个指向该文件的 FILE 指针，相当于获取一块内存区域，用来保存文件信息。

```c
FILE* fp;
```

上面示例定义了一个 FILE 指针`fp`。

下面是一个读取文件的完整示例。

```c
#include <stdio.h>

int main(void) {
  FILE* fp;
  char c;

  fp = fopen("hello.txt", "r");
  if (fp == NULL) {
    return -1;
  }

  c = fgetc(fp);
  printf("%c\n", c);

  fclose(fp);

  return 0;
}
```

上面示例中，新建文件指针`fp`以后，依次使用了下面三个文件操作函数，分成三个步骤。其他的文件操作，大致上也是这样的步骤。

第一步，使用`fopen()`打开指定文件，返回一个 File 指针。如果出错，返回 NULL。

它相当于将指定文件的信息与新建的文件指针`fp`相关联，在 FILE 结构内部记录了这样一些信息：文件内部的当前读写位置、读写报错的记录、文件结尾指示器、缓冲区开始位置的指针、文件标识符、一个计数器（统计拷贝进缓冲区的字节数）等等。后继的操作就可以使用这个指针（而不是文件名）来处理指定文件。

同时，它还为文件建立一个缓存区。由于存在缓存区，也可以说`fopen()`函数“打开一个了流”，后继的读写文件都是流模式。

第二步，使用读写函数，从文件读取数据，或者向文件写入数据。上例使用了`fgetc()`函数，从已经打开的文件里面，读取一个字符。

`fgetc()`一调用，文件的数据块先拷贝到缓冲区。不同的计算机有不同的缓冲区大小，一般是512字节或是它的倍数，如4096或16384。随着计算机硬盘容量越来越大，缓冲区也越来越大。

`fgetc()`从缓冲区读取数据，同时将文件指针内部的读写位置指示器，指向所读取字符的下一个字符。所有的文件读取函数都使用相同的缓冲区，后面再调用任何一个读取函数，都将从指示器指向的位置，即上一次读取函数停止的位置开始读取。

当读取函数发现已读完缓冲区里面的所有字符时，会请求把下一个缓冲区大小的数据块，从文件拷贝到缓冲区中。读取函数就以这种方式，读完文件的所有内容，直到文件结尾。不过，上例是只从缓存区读取一个字符。当函数在缓冲区里面，读完文件的最后一个字符时，就把 FILE 结构里面的文件结尾指示器设置为真。于是，下一次再调用读取函数时，会返回常量 EOF。EOF 是一个整数值，代表文件结尾，一般是`-1`。

第三步，`fclose()`关闭文件，同时清空缓存区。

上面是文件读取的过程，文件写入也是类似的方式，先把数据写入缓冲区，当缓冲区填满后，缓存区的数据将被转移到文件中。

## fopen()

`fopen()`函数用来打开文件。所有文件操作的第一步，都是使用`fopen()`打开指定文件。这个函数的原型定义在头文件`stdio.h`。

```c
FILE* fopen(char* filename, char* mode);
```

它接受两个参数。第一个参数是文件名(可以包含路径)，第二个参数是模式字符串，指定对文件执行的操作，比如下面的例子中，`r`表示以读取模式打开文件。

```c
fp = fopen("in.dat", "r");
```

成功打开文件以后，`fopen()`返回一个 FILE 指针，其他函数可以用这个指针操作文件。如果无法打开文件（比如文件不存在或没有权限），会返回空指针 NULL。所以，执行`fopen()`以后，最好判断一下，有没有打开成功。

```c
fp = fopen("hello.txt", "r");

if (fp == NULL) {
  printf("Can't open file!\n");
  exit(EXIT_FAILURE);
}
```

上面示例中，如果`fopen()`返回一个空指针，程序就会报错。

`fopen()`的模式字符串有以下几种。

- `r`：读模式，只用来读取数据。如果文件不存在，返回 NULL 指针。
- `w`：写模式，只用来写入数据。如果文件存在，文件长度会被截为0，然后再写入；如果文件不存在，则创建该文件。
- `a`：写模式，只用来在文件尾部追加数据。如果文件不存在，则创建该文件。
- `r+`：读写模式。如果文件存在，指针指向文件开始处，可以在文件头部添加数据。如果文件不存在，返回 NULL 指针。
- `w+`：读写模式。如果文件存在，文件长度会被截为0，然后再写入数据。这种模式实际上读不到数据，反而会擦掉数据。如果文件不存在，则创建该文件。
- `a+`：读写模式。如果文件存在，指针指向文件结尾，可以在现有文件末尾添加内容。如果文件不存在，则创建该文件。

上一小节说过，`fopen()`函数会为打开的文件创建一个缓冲区。读模式下，创建的是读缓存区；写模式下，创建的是写缓存区；读写模式下，会同时创建两个缓冲区。C 语言通过缓存区，以流的形式，向文件读写数据。

数据在文件里面，都是以二进制形式存储。但是，读取的时候，有不同的解读方法：以原本的二进制形式解读，叫做“二进制流”；将二进制数据转成文本，以文本形式解读，叫做“文本流”。写入操作也是如此，分成以二进制写入和以文本写入，后者会多一个文本转二进制的步骤。

`fopen()`的模式字符串，默认是以文本流读写。如果添加`b`后缀（表示 binary），就会以“二进制流”进行读写。比如，`rb`是读取二进制数据模式，`wb`是写入二进制数据模式。

模式字符串还有一个`x`后缀，表示独占模式（exclusive）。如果文件已经存在，则打开文件失败；如果文件不存在，则新建文件，打开后不再允许其他程序或线程访问当前文件。比如，`wx`表示以独占模式写入文件，如果文件已经存在，就会打开失败。

## 标准流

Linux 系统默认提供三个已经打开的文件，它们的文件指针如下。

- `stdin`（标准输入）：默认来源为键盘，文件指针编号为`0`。
- `stdout`（标准输出）：默认目的地为显示器，文件指针编号为`1`。
- `stderr`（标准错误）：默认目的地为显示器，文件指针编号为`2`。

Linux 系统的文件，不一定是数据文件，也可以是设备文件，即文件代表一个可以读或写的设备。文件指针`stdin`默认是把键盘看作一个文件，读取这个文件，就能获取用户的键盘输入。同理，`stdout`和`stderr`默认是把显示器看作一个文件，将程序的运行结果写入这个文件，用户就能看到运行结果了。它们的区别是，`stdout`写入的是程序的正常运行结果，`stderr`写入的是程序的报错信息。

这三个输入和输出渠道，是 Linux 默认提供的，所以分别称为标准输入（stdin）、标准输出（stdout）和标准错误（stderr）。因为它们的实现是一样的，都是文件流，所以合称为“标准流”。

Linux 允许改变这三个文件指针（文件流）指向的文件，这称为重定向（redirection）。

如果标准输入不绑定键盘，而是绑定其他文件，可以在文件名前面加上小于号`<`，跟在程序名后面。这叫做“输入重定向”（input redirection）。

```bash
$ demo < in.dat
```

上面示例中，`demo`程序代码里面的`stdin`，将指向文件`in.dat`，即从`in.dat`获取数据。

如果标准输出绑定其他文件，而不是显示器，可以在文件名前加上大于号`>`，跟在程序名后面。这叫做“输出重定向”（output redirection）。

```bash
$ demo > out.dat
```

上面示例中，`demo`程序代码里面的`stdout`，将指向文件`out.dat`，即向`out.dat`写入数据。

输出重定向`>`会先擦去`out.dat`的所有原有的内容，然后再写入。如果希望写入的信息追加在`out.dat`的结尾，可以使用`>>`符号。

```bash
$ demo >> out.dat
```

上面示例中，`demo`程序代码里面的`stdout`，将向文件`out.dat`写入数据。与`>`不同的是，写入的开始位置是`out.dat`的文件结尾。

标准错误的重定向符号是`2>`。其中的`2`代表文件指针的编号，即`2>`表示将2号文件指针的写入，重定向到`err.txt`。2号文件指针就是标准错误`stderr`。

```bash
$ demo > out.dat 2> err.txt
```

上面示例中，`demo`程序代码里面的`stderr`，会向文件`err.txt`写入报错信息。而`stdout`向文件`out.dat`写入。

输入重定向和输出重定向，也可以结合在一条命令里面。

```bash
$ demo < in.dat > out.dat

// or
$ demo > out.dat < in.dat
```

重定向还有另一种情况，就是将一个程序的标准输出`stdout`，指向另一个程序的标准输入`stdin`，这时要使用`|`符号。

```bash
$ random | sum
```

上面示例中，`random`程序代码里面的`stdout`的写入，会从`sum`程序代码里面的`stdin`被读取。

## fclose()

`fclose()`用来关闭已经使用`fopen()`打开的文件。它的原型定义在`stdio.h`。

```c
int fclose(FILE* stream);
```

它接受一个文件指针`fp`作为参数。如果成功关闭文件，`fclose()`函数返回整数`0`；如果操作失败（比如磁盘已满，或者出现 I/O 错误），则返回一个特殊值 EOF（详见下一小节）。

```c
if (fclose(fp) != 0)
  printf("Something wrong.");
```

不再使用的文件，都应该使用`fclose()`关闭，否则无法释放资源。一般来说，系统对同时打开的文件数量有限制，及时关闭文件可以避免超过这个限制。

## EOF

C 语言的文件操作函数的设计是，如果遇到文件结尾，就返回一个特殊值。程序接收到这个特殊值，就知道已经到达文件结尾了。

头文件`stdio.h`为这个特殊值定义了一个宏`EOF`（end of file 的缩写），它的值一般是`-1`。这是因为从文件读取的二进制值，不管作为无符号数字解释，还是作为 ASCII 码解释，都不可能是负值，所以可以很安全地返回`-1`，不会跟文件本身的数据相冲突。

需要注意的是，不像字符串结尾真的存储了`\0`这个值，`EOF`并不存储在文件结尾，文件中并不存在这个值，完全是文件操作函数发现到达了文件结尾，而返回这个值。

## freopen()

`freopen()`用于新打开一个文件，直接关联到某个已经打开的文件指针。这样可以复用文件指针。它的原型定义在头文件`stdio.h`。

```c
FILE* freopen(char* filename, char* mode, FILE stream);
```

它跟`fopen()`相比，就是多出了第三个参数，表示要复用的文件指针。其他两个参数都一样，分别是文件名和打开模式。

```c
freopen("output.txt", "w", stdout);
printf("hello");
```

上面示例将文件`output.txt`关联到`stdout`，此后向`stdout`写入的内容，都会写入`output.txt`。由于`printf()`默认就是输出到`stdout`，所以运行上面的代码以后，文件`output.txt`会被写入`hello`。

`freopen()`的返回值是它的第三个参数（文件指针）。如果打开失败（比如文件不存在），会返回空指针 NULL。

`freopen()`会自动关闭原先已经打开的文件，如果文件指针并没有指向已经打开的文件，则`freopen()`等同于`fopen()`。

下面是`freopen()`关联`scanf()`的例子。

```c
int i, i2;

scanf("%d", &i); 

freopen("someints.txt", "r", stdin);
scanf("%d", &i2);
```

上面例子中，一共调用了两次`scanf()`，第一次调用是从键盘读取，然后使用`freopen()`将`stdin`指针关联到某个文件，第二次调用就会从该文件读取。

某些系统允许使用`freopen()`，改变文件的打开模式。这时，`freopen()`的第一个参数应该是 NULL。

```c
freopen(NULL, "wb", stdout);
```

上面示例将`stdout`的打开模式从`w`改成了`wb`。

## fgetc()，getc()

`fgetc()`和`getc()`用于从文件读取一个字符。它们的用法跟`getchar()`类似，区别是`getchar()`只用来从`stdin`读取，而这两个函数是从任意指定的文件读取。它们的原型定义在头文件`stdio.h`。

```c
int fgetc(FILE *stream)
int getc(FILE *stream);
```

`fgetc()`与`getc()`的用法是一样的，都只有文件指针一个参数。两者的区别是，`getc()`一般用宏来实现，而`fgetc()`是函数实现，所以前者的性能可能更好一些。注意，虽然这两个函数返回的是一个字符，但是它们的返回值类型却不是`char`，而是`int`，这是因为读取失败的情况下，它们会返回 EOF，这个值一般是`-1`。

```c
#include <stdio.h>

int main(void) {
  FILE *fp;
  fp = fopen("hello.txt", "r");

  int c;
  while ((c = getc(fp)) != EOF)
    printf("%c", c);

  fclose(fp);
}
```

上面示例中，`getc()`依次读取文件的每个字符，将其放入变量`c`，直到读到文件结尾，返回 EOF，循环终止。变量`c`的类型是`int`，而不是`char`，因为有可能等于负值，所以设为`int`更好一些。

## fputc()，putc()

`fputc()`和`putc()`用于向文件写入一个字符。它们的用法跟`putchar()`类似，区别是`putchar()`是向`stdout`写入，而这两个函数是向文件写入。它们的原型定义在头文件`stdio.h`。

```c
int fputc(int char, FILE *stream);
int putc(int char, FILE *stream);
```

`fputc()`与`putc()`的用法是一样，都接受两个参数，第一个参数是待写入的字符，第二个参数是文件指针。它们的区别是，`putc()`通常是使用宏来实现，而`fputc()`只作为函数来实现，所以理论上，`putc()`的性能会好一点。

写入成功时，它们返回写入的字符；写入失败时，返回 EOF。

## fprintf()

`fprintf()`用于向文件写入格式化字符串，用法与`printf()`类似。区别是`printf()`总是写入`stdout`，而`fprintf()`则是写入指定的文件，它的第一个参数必须是一个文件指针。它的原型定义在头文件`stdio.h`。

```c
int fprintf(FILE* stream, const char* format, ...)
```

`fprintf()`可以替代`printf()`。

```c
printf("Hello, world!\n");
fprintf(stdout, "Hello, world!\n");
```

上面例子中，指定`fprintf()`写入`stdout`，结果就等同于调用`printf()`。

```c
fprintf(fp, "Sum: %d\n", sum);
```

上面示例是向文件指针`fp`写入指定格式的字符串。

下面是向`stderr`输出错误信息的例子。

```c
fprintf(stderr, "Something number.\n");
```

## fscanf()

`fscanf()`用于按照给定的模式，从文件中读取内容，用法跟`scanf()`类似。区别是`scanf()`总是从`stdin`读取数据，而`fscanf()`是从文件读入数据，它的原型定义在头文件`stdio.h`，第一个参数必须是文件指针。

```c
int fscanf(FILE* stream, const char* format, ...);
```

下面是一个例子。

```c
fscanf(fp, "%d%d", &i, &j);
```

上面示例中，`fscanf()`从文件`fp`里面，读取两个整数，放入变量`i`和`j`。

使用`fscanf()`的前提是知道文件的结构，它的占位符解析规则与`scanf()`完全一致。由于`fscanf()`可以连续读取，直到读到文件尾，或者发生错误（读取失败、匹配失败），才会停止读取，所以`fscanf()`通常放在循环里面。

```c
while(fscanf(fp, "%s", words) == 1)
  puts(words);
```

上面示例中，`fscanf()`依次读取文件的每个词，将它们一行打印一个，直到文件结束。

`fscanf()`的返回值是赋值成功的变量数量，如果赋值失败会返回 EOF。

## fgets()

`fgets()`用于从文件读取指定长度的字符串，它名字的第一个字符是`f`，就代表`file`。它的原型定义在头文件`stdio.h`。

```c
char* fgets(char* str, int STRLEN, File* fp);
```

它的第一个参数`str`是一个字符串指针，用于存放读取的内容。第二个参数`STRLEN`指定读取的长度，第三个参数是一个 FILE 指针，指向要读取的文件。

`fgets()`读取 STRLEN - 1 个字符之后，或者遇到换行符与文件结尾，就会停止读取，然后在已经读取的内容末尾添加一个空字符`\0`，使之成为一个字符串。注意，`fgets()`会将换行符（`\n`）存储进字符串。

如果`fgets`的第三个参数是`stdin`，就可以读取标准输入，等同于`scanf()`。

```c
fgets(str, sizeof(str), stdin);
```

读取成功时，`fgets()`的返回值是它的第一个参数，即指向字符串的指针，否则返回空指针 NULL。

`fgets()`可以用来读取文件的每一行，下面是读取文件所有行的例子。

```c
#include <stdio.h>

int main(void) {
  FILE* fp;
  char s[1024];  // 数组必须足够大，足以放下一行
  int linecount = 0;

  fp = fopen("hello.txt", "r");

  while (fgets(s, sizeof s, fp) != NULL)
    printf("%d: %s", ++linecount, s);

  fclose(fp);
}
```

上面示例中，每读取一行，都会输出行号和该行的内容。

下面的例子是循环读取用户的输入。

```c
char words[10];

puts("Enter strings (q to quit):");

while (fgets(words, 10, stdin) != NULL) {
  if (words[0] == 'q' && words[1] == '\n')
    break;

  puts(words);
}

puts("Done.");
```

上面的示例中，如果用户输入的字符串大于9个字符，`fgets()`会多次读取。直到遇到`q` + 回车键，才会退出循环。

## fputs()

`fputs()`函数用于向文件写入字符串，和`puts()`函数只有一点不同，那就是它不会在字符串末尾添加换行符。这是因为`fgets()`保留了换行符，所以`fputs()`就不添加了。`fputs()`函数通常与`fgets()`配对使用。

它的原型定义在`stdio.h`。

```c
int fputs(const char* str, FILE* stream);
```

它接受两个参数，第一个参数是字符串指针，第二个参数是要写入的文件指针。如果第二个参数为`stdout`（标准输出），就是将内容输出到计算机屏幕，等同于`printf()`。

```c
char words[14];

puts("Enter a string, please.");
fgets(words, 14, stdin);

puts("This is your string:");
fputs(words, stdout);
```

上面示例中，先用`fgets()`从`stdin`读取用户输入，然后用`fputs()`输出到`stdout`。

写入成功时，`fputs()`返回一个非负整数，否则返回 EOF。

## fwrite()

`fwrite()`用来一次性写入较大的数据块，主要用途是将数组数据一次性写入文件，适合写入二进制数据。它的原型定义在`stdio.h`。

```c
size_t fwrite(
  const void* ptr,
  size_t size,
  size_t nmemb,
  FILE* fp
);
```

它接受四个参数。

- `ptr`：数组指针。
- `size`：每个数组成员的大小，单位字节。
- `nmemb`：数组成员的数量。
- `fp`：要写入的文件指针。

注意，`fwrite()`原型的第一个参数类型是`void*`，这是一个无类型指针，编译器会自动将参数指针转成`void*`类型。正是由于`fwrite()`不知道数组成员的类型，所以才需要知道每个成员的大小（第二个参数）和成员数量（第三个参数）。

`fwrite()`函数的返回值是成功写入的数组成员的数量（注意不是字节数）。正常情况下，该返回值就是第三个参数`nmemb`，但如果出现写入错误，只写入了一部分成员，返回值会比`nmemb`小。

要将整个数组`arr`写入文件，可以采用下面的写法。

```c
fwrite(
  arr,
  sizeof(arr[0]),
  sizeof(arr) / sizeof(arr[0]),
  fp
);
```

上面示例中，`sizeof(a[0])`是每个数组成员占用的字节，`sizeof(a) / sizeof(a[0])`是整个数组的成员数量。

下面的例子是将一个大小为256字节的字符串写入文件。

```c
char buffer[256];

fwrite(buffer, 1, 256, fp);
```

上面示例中，数组`buffer`每个成员是1个字节，一共有256个成员。由于`fwrite()`是连续内存复制，所以写成`fwrite(buffer, 256, 1, fp)`也能达到目的。

`fwrite()`没有规定一定要写入整个数组，只写入数组的一部分也是可以的。

任何类型的数据都可以看成是1字节数据组成的数组，或者是一个成员的数组，所以`fwrite()`实际上可以写入任何类型的数据，而不仅仅是数组。比如，`fwrite()`可以将一个 Struct 结构写入文件保存。

```c
fwrite(&s, sizeof(s), 1, fp);
```

上面示例中，`s`是一个 Struct 结构指针，可以看成是一个成员的数组。注意，如果`s`的属性包含指针，存储时需要小心，因为保存指针可能没意义，还原出来的时候，并不能保证指针指向的数据还存在。

`fwrite()`以及后面要介绍的`fread()`，比较适合读写二进制数据，因为它们不会对写入的数据进行解读。二进制数据可能包含空字符`\0`，这是 C 语言的字符串结尾标记，所以读写二进制文件，不适合使用文本读写函数（比如`fprintf()`等）。

下面是一个写入二进制文件的例子。

```c
#include <stdio.h>

int main(void) {
  FILE* fp;
  unsigned char bytes[] = {5, 37, 0, 88, 255, 12};

  fp = fopen("output.bin", "wb");
  fwrite(bytes, sizeof(char), sizeof(bytes), fp);
  fclose(fp);
  return 0;
}
```

上面示例中，写入二进制文件时，`fopen()`要使用`wb`模式打开，表示二进制写入。`fwrite()`可以把数据解释成单字节数组，因此它的第二个参数是`sizeof(char)`，第三个参数是数组的总字节数`sizeof(bytes)`。

上面例子写入的文件`output.bin`，使用十六进制编辑器打开，会是下面的内容。

```c
05 25 00 58 ff 0c
```

`fwrite()`还可以连续向一个文件写入数据。

```c
struct clientData myClient = {1, 'foo bar'};

for (int i = 1; i <= 100; i++) {
  fwrite(&myClient, sizeof(struct clientData), 1, cfPtr);
}
```

上面示例中，`fwrite()`连续将100条数据写入文件。

## fread()

`fread()`函数用于一次性从文件读取较大的数据块，主要用途是将文件内容读入一个数组，适合读取二进制数据。它的原型定义在头文件`stdio.h`。

```c
size_t fread(
  void* ptr,
  size_t size,
  size_t nmemb,
  FILE* fp
);
```

它接受四个参数，与`fwrite()`完全相同。

- `ptr`：数组地址。
- `size`：每个数组成员的大小，单位为字节。
- `nmemb`：数组的成员数量。
- `fp`：文件指针。

要将文件内容读入数组`arr`，可以采用下面的写法。

```c
fread(
  arr,
  sizeof(arr[0]),
  sizeof(arr) / sizeof(arr[0]),
  fp
);
```

上面示例中，数组长度（第二个参数）和每个成员的大小（第三个参数）的乘积，就是数组占用的内存空间的大小。`fread()`会从文件（第四个参数）里面读取相同大小的内容，然后将`ptr`（第一个参数）指向这些内容的内存地址。

下面的例子是将文件内容读入一个10个成员的双精度浮点数数组。

```c
double earnings[10];
fread(earnings, sizeof(double), 10, fp);
```

上面示例中，每个数组成员的大小是`sizeof(double)`，一个有10个成员，就会从文件`fp`读取`sizeof(double) * 10`大小的内容。

`fread()`函数的返回值是成功读取的数组成员的数量。正常情况下，该返回值就是第三个参数`nmemb`，但如果出现读取错误或读到文件结尾，该返回值就会比`nmemb`小。所以，检查`fread()`的返回值是非常重要的。

`fread()`和`fwrite()`可以配合使用。在程序终止之前，使用`fwrite()`将数据保存进文件，下次运行时再用`fread()`将数据还原进入内存。

下面是读取上一节生成的二进制文件`output.bin`的例子。

```c
#include <stdio.h>

int main(void) {
  FILE* fp;
  unsigned char c;

  fp = fopen("output.bin", "rb");
  while (fread(&c, sizeof(char), 1, fp) > 0)
    printf("%d\n", c);
  return 0;
}
```

运行后，得到如下结果。

```c
5
37
0
88
255
12
```

## feof()

`feof()`函数判断文件的内部指针是否指向文件结尾。它的原型定义在头文件`stdio.h`。

```c
int feof(FILE *fp);
```

`feof()`接受一个文件指针作为参数。如果已经到达文件结尾，会返回一个非零值（表示 true），否则返回`0`（表示 false）。

诸如`fgetc()`这样的文件读取函数，如果返回 EOF，有两种可能，一种可能是已读取到文件结尾，另一种可能是出现读取错误。`feof()`可以用来判断到底是那一种情况。

下面是通过`feof()`判断是否到达文件结尾，从而循环读取整个文件的例子。

```c
int num;
char name[50];

FILE* cfPtr = fopen("clients.txt", "r");

while (!feof(cfPtr)) {
  fscanf(cfPtr, "%d%s\n", &num, name);
  printf("%d %s\n", num, name);
}

fclose(cfPtr);
```

上面示例通过循环判断`feof()`是否读到文件结尾，从而实现读出整个文件内容。

`feof()`为真时，可以通过`fseek()`、`rewind()`、`fsetpos()`函数改变文件内部读写位置的指示器，从而清除这个函数的状态。

## fseek()

每个文件指针都有一个内部指示器（内部指针），记录当前打开的文件的读写位置（file position），即下一次读写从哪里开始。文件操作函数（比如`getc()`、`fgets()`、`fscanf()`和`fread()`等）都从这个指示器指定的位置开始按顺序读写文件。

如果希望改变这个指示器，将它移到文件的指定位置，可以使用`fseek()`函数。它的原型定义在头文件`stdio.h`。

```c
int fseek(FILE* stream, long int offset, int whence);
```

`fseek()`接受3个参数。

- `stream`：文件指针。
- `offset`：距离基准（第三个参数）的字节数。类型为 long int，可以为正值（向文件末尾移动）、负值（向文件开始处移动）或 0（保持不动）。
- `whence`：位置基准，用来确定计算起点。它的值是以下三个宏（定义在`stdio.h`）：`SEEK_SET`（文件开始处）、`SEEK_CUR `（内部指针的当前位置）、`SEEK_END`（文件末尾）

请看下面的例子。

```c
// 定位到文件开始处
fseek(fp, 0L, SEEK_SET);

// 定位到文件末尾
fseek(fp, 0L, SEEK_END);

// 从当前位置后移2个字节
fseek(fp, 2L, SEEK_CUR);

// 定位到文件第10个字节
fseek(fp, 10L, SEEK_SET);

// 定位到文件倒数第10个字节
fseek(fp, -10L, SEEK_END);
```

上面示例中，`fseek()`的第二个参数为 long 类型，所以移动距离必须加上后缀`L`，将其转为 long 类型。

下面的示例逆向输出文件的所有字节。

```c
for (count = 1L; count <= size; count++) {
  fseek(fp, -count, SEEK_END);
  ch = getc(fp);
}
```

注意，`fseek()`最好只用来操作二进制文件，不要用来读取文本文件。因为文本文件的字符有不同的编码，某个位置的准确字节位置不容易确定。

正常情况下，`fseek()`的返回值为0。如果发生错误（如移动的距离超出文件的范围），返回值为非零值（比如`-1`)。

## ftell()

`ftell()`函数返回文件内部指示器的当前位置。它的原型定义在头文件`stdio.h`。

```c
long int ftell(FILE* stream);
```

它接受一个文件指针作为参数。返回值是一个 long 类型的整数，表示内部指示器的当前位置，即文件开始处到当前位置的字节数，`0`表示文件开始处。如果发生错误，`ftell()`返回`-1L`。

`ftell()`可以跟`fseek()`配合使用，先记录内部指针的位置，一系列操作过后，再用`fseek()`返回原来的位置。

```c
long file_pos = ftell(fp);

// 一系列文件操作之后
fseek(fp, file_pos, SEEK_SET);
```

下面的例子先将指示器定位到文件结尾，然后得到文件开始处到结尾的字节数。

```c
fseek(fp, 0L, SEEK_END);
size = ftell(fp);
```

## rewind()

`rewind()`函数可以让文件的内部指示器回到文件开始处。它的原型定义在`stdio.h`。

```c
void rewind(file* stream);
```

它接受一个文件指针作为参数。

`rewind(fp)`基本等价于`fseek(fp, 0l, seek_set)`，唯一的区别是`rewind()`没有返回值，而且会清除当前文件的错误指示器。

## fgetpos()，fsetpos()

`fseek()`和`ftell()`有一个潜在的问题，那就是它们都把文件大小限制在 long int 类型能表示的范围内。这看起来相当大，但是在32位计算机上，long int 的长度为4个字节，能够表示的范围最大为 4GB。随着存储设备的容量迅猛增长，文件也越来越大，往往会超出这个范围。鉴于此，C 语言新增了两个处理大文件的新定位函数：`fgetpos()`和`fsetpos()`。

它们的原型都定义在头文件`stdio.h`。

```c
int fgetpos(FILE* stream, fpos_t* pos);
int fsetpos(FILE* stream, const fpos_t* pos);
```

`fgetpos()`函数会将文件内部指示器的当前位置，存储在指针变量`pos`。该函数接受两个参数，第一个是文件指针，第二个存储指示器位置的变量。

`fsetpos()`函数会将文件内部指示器的位置，移动到指针变量`pos`指定的地址。注意，变量`pos`必须是通过调用`fgetpos()`方法获得的。`fsetpos()`的两个参数与`fgetpos()`必须是一样的。

记录文件内部指示器位置的指针变量`pos`，类型为`fpos_t*`（file position type 的缩写，意为文件定位类型）。它不一定是整数，也可能是一个 Struct 结构。

下面是用法示例。

```c
fpos_t file_pos;
fgetpos(fp, &file_pos);

// 一系列文件操作之后
fsetpos(fp, &file_pos);
```

上面示例中，先用`fgetpos()`获取内部指针的位置，后面再用`fsetpos()`恢复指针的位置。

执行成功时，`fgetpos()`和`fsetpos()`都会返回`0`，否则返回非零值。

## ferror()，clearerr()

所有的文件操作函数如果执行失败，都会在文件指针里面记录错误状态。后面的操作只要读取错误指示器，就知道前面的操作出错了。

`ferror()`函数用来返回错误指示器的状态。可以通过这个函数，判断前面的文件操作是否成功。它的原型定义在头文件`stdio.h`。

```c
int ferror(FILE *stream);
```

它接受一个文件指针作为参数。如果前面的操作出现错误，`ferror()`就会返回一个非零整数（表示 true），否则返回`0`。

`clearerr()`函数用来重置出错指示器。它的原型定义在头文件`stdio.h`。

```c
void clearerr(FILE* fp);
```

它接受一个文件指针作为参数，没有返回值。

下面是一个例子。

```c
FILE* fp = fopen("file.txt", "w");
char c = fgetc(fp);

if (ferror(fp)) {
  printf("读取文件：file.txt 时发生错误\n");
}

clearerr(fp);
```

上面示例中，`fgetc()`尝试读取一个以”写模式“打开的文件，读取失败就会返回 EOF。这时调用`ferror()`就可以知道上一步操作出错了。处理完以后，再用`clearerr()`清除出错状态。

文件操作函数如果正常执行，`ferror()`和`feof()`都会返回零。如果执行不正常，就要判断到底是哪里出了问题。

```c
if (fscanf(fp, "%d", &n) != 1) {
  if (ferror(fp)) {
    printf("io error\n");
  }
  if (feof(fp)) {
    printf("end of file\n");
  }

  clearerr(fp);

  fclose(fp);
}
```

上面示例中，当`fscanf()`函数报错时，通过检查`ferror()`和`feof()`，确定到底发生什么问题。这两个指示器改变状态后，会保持不变，所以要用`clearerr()`清除它们，`clearerr()`可以同时清除两个指示器。

## remove()

`remove()`函数用于删除指定文件。它的原型定义在头文件`stdio.h`。

```c
int remove(const char* filename);
```

它接受文件名作为参数。如果删除成功，`remove()`返回`0`，否则返回非零值。

```c
remove("foo.txt");
```

上面示例删除了`foo.txt`文件。

注意，删除文件必须是在文件关闭的状态下。如果是用`fopen()`打开的文件，必须先用`fclose()`关闭后再删除。

## rename()

`rename()`函数用于文件改名，也用于移动文件。它的原型定义在头文件`stdio.h`。

```c
int rename(const char* old_filename, const char* new_filename);
```

它接受两个参数，第一个参数是现在的文件名，第二个参数是新的文件名。如果改名成功，`rename()`返回`0`，否则返回非零值。

```c
rename("foo.txt", "bar.txt");
```

上面示例将`foo.txt`改名为`bar.txt`。

注意，改名后的文件不能与现有文件同名。另外，如果要改名的文件已经打开了，必须先关闭，然后再改名，对打开的文件进行改名会失败。

下面是移动文件的例子。

```c
rename("/tmp/evidence.txt", "/home/beej/nothing.txt");
```
