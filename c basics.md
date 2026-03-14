# C 語言基礎語法

## 1. 資料型別
C 語言提供多種基本資料型別，用於儲存不同種類的資料

### 基本資料型別
- **int**: 整數型別 (e.g., `int a = 1;`)
- **float**: 單精度浮點數 (e.g., `float b = 10.5f;`)
- **double**: 雙精度浮點數 (e.g., `double c = 10.5;`)
- **char**: 字元型別 (e.g., `char d = 'A';`)

### 型別修飾詞
- **short**: 短整數
- **long**: 長整數
- **unsigned**: 無符號
- **signed**: 有符號

```c
#include <stdio.h>

int main()
{
    // 基本型別
    int a = 100;
    float b = 10.5f;
    double c = 10.5;
    char d = 'A';

    // 型別修飾
    short e = 10;
    long f = 100000L;
    unsigned int g = 200;
    signed int h = -50;

    printf("int a = %d\n", a);
    printf("float b = %f\n", b);
    printf("double c = %lf\n", c);
    printf("char d = %c\n", d);

    printf("short e = %hd\n", e);
    printf("long f = %ld\n", f);
    printf("unsigned int g = %u\n", g);
    printf("signed int h = %d\n", h);

    //檢查型別大小
    printf("char: %zu bytes\n", sizeof(char));
    printf("short: %zu bytes\n", sizeof(short));
    printf("int: %zu bytes\n", sizeof(int));
    printf("long: %zu bytes\n", sizeof(long));
    printf("float: %zu bytes\n", sizeof(float));
    printf("double: %zu bytes\n", sizeof(double));

    return 0;
}
```

## printf 常見格式

| 型別 | printf 格式 |
|-----|-------------|
| int | `%d` |
| float | `%f` |
| double | `%lf` |
| char | `%c` |
| short | `%hd` |
| long | `%ld` |
| unsigned int | `%u` |
