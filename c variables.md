# C 語言基礎語法

## 1. 資料型別
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

##  運算子、if、for、while、array、char、string 視情況補充
##  2. 指標 (Pointer)
### 什麼是指標
指標是一個變數，用來儲存另一個變數的記憶體位址。
### 單重指標

```c
int main() {
    // 單重指標範例
    int a = 10;
    int *ptr = &a;  // ptr 儲存 a 的記憶體位址
                     // 習慣念法: "ptr 指向 a 的位置"

    printf("a = %d\n", *ptr);  // 解參照，取得 a 的值
                                // 習慣念法: "ptr 指向 a 裡面的值"

    // 額外示範 ptr 與 &ptr 的差別
    printf("ptr 指向的位址 (a 的位置) = %p\n", ptr); //ptr指向的位置
    printf("ptr 本身的位址 = %p\n", &ptr); //ptr自己本身的位置

    return 0;
}
```
> 補充說明  
> ptr 儲存 a 的位址  
> *ptr 取得 a 的值  
> &ptr 取得 ptr 本身的位址  
### 雙重指標 (Pointer to Pointer)
```c
#include <stdio.h>

int main() {
    int a = 10;
    int *ptr = &a;       // ptr 指向 a
    int **dptr = &ptr;   // dptr 指向 ptr

    printf("a = %d\n", **dptr);  // 解兩層指標
    return 0;
}
```

### 指標與陣列的運算差異
假設有陣列：
```c
int arr[3] = {10, 20, 30};
int *p = arr;  // 指向陣列首元素
                // 陣列退化：arr 會自動轉成指向首元素的指標
                // 也可以寫成 int *p = &arr[0];
```
| 表達式       | 意義 (較簡單理解)                                           | 範例值                          |
|-------------|--------------------------------------------------------|--------------------------------|
| *(&p + 1)   | 將指標變數 p 當作一個單位，位移整個陣列大小到陣列結尾後的位置（通常不建議使用） | 未定義，依系統而異             |
| *(p + 1)    | p 指向的位置往後偏移 1，取得下一個陣列元素的值          | 20                             |
| *(p++)      | 先解參照 p 所指向的值，再將 p 指向下一個元素           | 10（取完後 p 指向 arr[1]）     |
| *(++p)      | 先將 p 往後移動 1，再解參照取得該位置的值              | 20（p 往前移，然後取值）       |

### 小補充：陣列退化（Array Decay）

陣列名稱在大多數表達式中會自動轉換為指向首元素的指標（稱為「陣列退化」）。
```c
int arr[3] = {10, 20, 30};
int *p1 = arr;       // 指向陣列首元素
int *p2 = &arr[0];   // 等價寫法
```
> 注意：&arr 與 arr 有差別   
> &arr 型別是 int (*)[3] → 指向整個陣列     
> arr 型別是 int* → 指向第一個元素
