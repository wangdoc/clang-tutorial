# inttypes.h

C 语言还在头文件 inttypes.h 里面，为 stdint.h 定义的四类整数类型，提供了`printf()`和`scanf()`的占位符。

- 固定宽度整数类型，比如 int8_t。
- 最小宽度整数类型，比如 int_least8_t。
- 最快最小宽度整数类型，比如 int_fast8_t。
- 最大宽度整数类型，比如 intmax_t。

`printf()`的占位符采用`PRI + 原始占位符 + 类型关键字/宽度`的形式构成。举例来说，原始占位符为`%d`，则对应的占位符如下。

- PRIdn （固定宽度类型）
- PRIdLEASTn （最小宽度类型）
- PRIdFASTn （最快最小宽度类型）
- PRIdMAX （最大宽度类型）

上面占位符中的`n`，可以用8、16、32、64代入。

下面是用法示例。

```c
#include <stdio.h>
#include <stdint.h>
#include <inttypes.h>

int main(void) {
  int_least16_t x = 3490;
  printf("The value is %" PRIdLEAST16 "!\n", x);
}
```

上面示例中，`PRIdLEAST16`对应的整数类型为 int_least16_t，原始占位符为`%d`。另外，`printf()`的第一个参数用到了多个字符串自动合并的写法。

下面是其它的原始占位符对应的占位符。

- %i：PRIin    PRIiLEASTn    PRIiFASTn    PRIiMAX
- %o：PRIon    PRIoLEASTn    PRIoFASTn    PRIoMAX
- %u：PRIun    PRIuLEASTn    PRIuFASTn    PRIuMAX
- %x：PRIxn    PRIxLEASTn    PRIxFASTn    PRIxMAX
- %X：PRIXn    PRIXLEASTn    PRIXFASTn    PRIXMAX

`scanf()`的占位符规则也与之类似。

- %d：SCNdn    SCNdLEASTn    SCNdFASTn    SCNdMAX
- %i：SCNin    SCNiLEASTn    SCNiFASTn    SCNiMAX
- %o：SCNon    SCNoLEASTn    SCNoFASTn    SCNoMAX
- %u：SCNun    SCNuLEASTn    SCNuFASTn    SCNuMAX
- %x：SCNxn    SCNxLEASTn    SCNxFASTn    SCNxMAX
