# C 語言基礎語法

##  資料型別
C 語言提供多種基本資料型別，用於儲存不同種類的資料。

### 基本資料型別
- **int**: 整數型別 (e.g., `int a = 1;`)
- **float**: 單精度浮點數 (e.g., `float b = 1.5f;`)
- **double**: 雙精度浮點數 (e.g., `double c = 1.5;`)
- **char**: 字元型別 (e.g., `char d = 'A';`)

### 型別修飾詞 (Type Modifiers)
- **short**: 短整數
- **long**: 長整數
- **unsigned**: 無符號
- **signed**: 有符號

### 常數 (Constants)

C 提供兩種方式定義常數：

1️⃣ **巨集 (Macro)**
巨集是在 **編譯前處理階段**被替換的文字，沒有型別檢查，只是純文字替換。適合用於條件編譯或需要編譯前決定的常數。
```c
#define MAX_SIZE 100  // 前置處理器定義常數
```
2️⃣ **const 常數**
const 定義的變數在編譯期是固定的值，有型別，並且可以在程式中被型別檢查。適合用於程式內的安全常數。 
```c
const int MIN_SIZE = 10;  // 編譯期常數
```
### 巨集括號問題
```c
#define SQUARE(x) x*x

int a = 3;
int b = SQUARE(a+1); // 預期 16？實際結果 7
```
原因：巨集只是文字替換，沒有加括號會改變運算順序。

```c
#define SQUARE(x) ((x)*(x))
int b = SQUARE(a+1); // 正確 16
```

### const 無法用於 #if
```c
const int MAX = 100;

#if MAX > 50 // 錯誤！因為 #if 在編譯前處理器階段，無法使用 const
#endif
```
> 建議：  
> 巨集：用於條件編譯或硬體暫存器位址  
> const：用於程式中一般常數，型別安全

### 簡單程式碼
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
    //有興趣可以自己跑跑看
    printf("char: %zu bytes\n", sizeof(char));
    printf("short: %zu bytes\n", sizeof(short));
    printf("int: %zu bytes\n", sizeof(int));
    printf("long: %zu bytes\n", sizeof(long));
    printf("float: %zu bytes\n", sizeof(float));
    printf("double: %zu bytes\n", sizeof(double));

    return 0;
}
```
### 常見資料型別大小與範圍 (Typical 64-bit system)

| 型別 | Size (bytes) | 範圍 |
|-----|-------------|------|
| char | 1 | -128 ~ 127 |
| unsigned char | 1 | 0 ~ 255 |
| short | 2 | -32,768 ~ 32,767 |
| unsigned short | 2 | 0 ~ 65,535 |
| int | 4 | -2,147,483,648 ~ 2,147,483,647 |
| unsigned int | 4 | 0 ~ 4,294,967,295 |
| long | 8 | 約 ±9.22 × 10¹⁸ |
| float | 4 | 約 ±3.4 × 10³⁸ |
| double | 8 | 約 ±1.7 × 10³⁰⁸ |

> Note  
> C standard does not strictly define the size of each type.  
> The values above are typical on modern 64-bit systems.

### printf 常見格式

| 型別 | printf 格式 |
|-----|-------------|
| int | `%d` |
| float | `%f` |
| double | `%lf` |
| char | `%c` |
| short | `%hd` |
| long | `%ld` |
| unsigned int | `%u` |
| long long | `%lld` |
| unsigned long | `%lu` |
| pointer | `%p` |
| string | `%s` |

