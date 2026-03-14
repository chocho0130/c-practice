##   指標 (Pointer)
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
    printf("a 的位址 = %p\n", &a);                  // a 本身的位址
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

    // ===== 變數 a =====
    printf("[變數] a 的值: %d\n", a);            // 預期輸出: 10
    printf("[變數] a 的位址: %p\n", (void*)&a);  // 預期輸出: a 的記憶體位址

    // ===== 單指標 ptr =====
    printf("[指標] ptr 指向的值 (*ptr): %d\n", *ptr);      // 預期輸出: 10
    printf("[指標] ptr 儲存的位址 (ptr): %p\n", (void*)ptr);  // 預期輸出: a 的位址
    printf("[指標] ptr 本身的位址 (&ptr): %p\n", (void*)&ptr); // 預期輸出: ptr 的位址

    // ===== 雙指標 dptr =====
    printf("[雙指標] dptr 指向的值 (*dptr): %p\n", (void*)*dptr);   // 預期輸出: ptr 的位址
    printf("[雙指標] dptr 指向的值再解參照 (**dptr): %d\n", **dptr); // 預期輸出: 10
    printf("[雙指標] dptr 儲存的位址 (dptr): %p\n", (void*)dptr);      // 預期輸出: ptr 的位址
    printf("[雙指標] dptr 本身的位址 (&dptr): %p\n", (void*)&dptr);    // 預期輸出: dptr 的位址

    return 0;
}
```
> 詳細參考這個連結，他的圖片挺好記憶 https://hackmd.io/@ndhu-programming-2021/HkruR89RY

為什麼要 (void*)？ 

標準 C 語言要求：
- %p 只保證正確打印 void*，其他型別不保證
- 避免警告：特別是用 -Wall -Wextra 編譯時
- 可讀性：清楚表明「這是要輸出記憶體位址」

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
