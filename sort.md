## Sort (排序)

- Selection Sort (選擇排序)：從還沒排好的數字中，「挑出最小的」放到最前面。重複這個動作，直到所有數字都排好。
- Insertion Sort (插入排序)：像「玩大老二理牌」。把新拿到的牌，插入到左手已經排好序的正確位置中。
- Bubble Sort (泡沫排序)：相鄰的兩個數兩兩比較，「大的往後移」。每一輪最高的那個人（最大值）就會像泡泡一樣浮到最後面。
- Quick Sort (快速排序)：選一個數字當「基準點 (Pivot)」，把比它小的放左邊，比它大的放右邊，然後左右兩邊各自再重複這個動作（遞迴）。
- Merge Sort (合併排序) : Merge Sort 透過遞迴對半拆分陣列至單一元素，再依序將有序小組兩兩合併以完成排序。

### 以 C array 呈現

### 1. Bubble Sort (泡沫排序)  
這個是最基本的
```c
void bubbleSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (arr[j] > arr[j + 1]) {
                int tmp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = tmp;
            }
        }
    }
}
```
### 2. Selection Sort (選擇排序)
會泡沫排序後，選擇排序改一改就可以出來
```c
void selectionSort(int arr[], int n) {
    for (int i = 0; i < n - 1; i++) {
        int min_idx = i;
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[min_idx]) min_idx = j;
        }
        //最後才交換，由這邊可以看到不穩定的原因
        int tmp = arr[min_idx];
        arr[min_idx] = arr[i];
        arr[i] = tmp;
    }
}
```
### 3. Insertion Sort (插入排序)
相比之下比較喜歡泡沫排序
```c
void insertionSort(int arr[], int n) {
    for (int i = 1; i < n; i++) {
        int key = arr[i]; // 取出當前的牌
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j]; // 往後挪位子
            j--;
        }
        arr[j + 1] = key; // 插入正確位置
    }
}
```

### 4. Quick Sort (快速排序)
挺常考的排序，要花一點時間記
```c
void swap(int* a, int* b) {
    int t = *a;
    *a = *b;
    *b = t;
}

int partition(int arr[], int low, int high) {
    int pivot = arr[high]; // 選最後一個當基準
    int i = (low - 1); 
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            swap(&arr[i], &arr[j]);
        }
    }
    swap(&arr[i + 1], &arr[high]);
    return (i + 1);
}

void quickSort(int arr[], int low, int high) {
    if (low < high) {
        int pi = partition(arr, low, high);
        quickSort(arr, low, pi - 1); // 遞迴左半部
        quickSort(arr, pi + 1, high); // 遞迴右半部
    }
}
```
### 5. Merge Sort (合併排序) 
這也是很常考的排序，要花一點時間記
```c
void merge(int arr[], int l, int m, int r) {
    int n1 = m - l + 1;
    int n2 = r - m;
    int L[n1], R[n2]; // 建立左右兩個暫存陣列

    for (int i = 0; i < n1; i++) L[i] = arr[l + i];
    for (int j = 0; j < n2; j++) R[j] = arr[m + 1 + j];

    int i = 0, j = 0, k = l;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) { arr[k] = L[i]; i++; }
        else { arr[k] = R[j]; j++; }
        k++;
    }
    while (i < n1) { arr[k] = L[i]; i++; k++; } // 搬移剩餘元素
    while (j < n2) { arr[k] = R[j]; j++; k++; }
}

void mergeSort(int arr[], int l, int r) {
    if (l < r) {
        int m = l + (r - l) / 2;
        mergeSort(arr, l, m);     // 遞迴拆分左半邊
        mergeSort(arr, m + 1, r); // 遞迴拆分右半邊
        merge(arr, l, m, r);      // 合併
    }
}
```
### 排序演算法速查表 

> **穩定性 (Stability)**  
> 如果兩個數字的值一樣，排序後它們的「相對順序」會不會改變？  
> - **穩定 (Stable)**：原本 A 在 B 前面（且 A = B），排序後 A 一定還是在 B 前面。  
>   - **應用例子**：先按「日期」排好，再按「金額」排，穩定演算法能保證金額相同時，日期還是由舊到新。  
> - **不穩定 (Unstable)**：排序過程中可能發生大跨度的跳躍，導致原本在前面的 A 跑到 B 後面去了。

> **空間複雜度 (Space Complexity)**  
> 執行演算法時，「額外需要多少記憶體空間」。  
> - `O(1)`：不需要額外空間，直接在原本的陣列裡搬移（原地執行）。  
> - `O(log n)`：像 Quick Sort，需要遞迴，每一層切分會佔用 Stack 記憶體。

> **時間複雜度 (Time Complexity)**  
> 計算「基本操作（如比較或交換）執行了幾次」。  
> - `O(n^2)`：想像一個 n 個數字的陣列，第一輪看 n 個，第二輪看 n-1 個…最後看 1 個，總次數 = n + (n-1) + … + 1 = n(n+1)/2 → 簡化成 O(n²)。  
> - `O(n log n)`：Quick Sort 每次把陣列切一半（log n 層），每層掃過 n 個數字，總共 n × log n。

| 演算法 (Algorithm)           | 最佳時間 (Best)                            | 平均時間 (Average)                          | 最壞時間 (Worst)                            | 空間複雜度 (Space)           | 穩定性 (Stability)        |
|------------------------------|-------------------------------------------|--------------------------------------------|-------------------------------------------|-------------------------------|---------------------------|
| Selection Sort (選擇排序)    | <span style="color:red">O(n²) 慢</span>  | <span style="color:red">O(n²) 慢</span>   | <span style="color:red">O(n²) 慢</span>  | <span style="color:green">O(1) 小</span> | <span style="color:red">不穩定</span> |
| Insertion Sort (插入排序)    | <span style="color:green">O(n) 快</span> | <span style="color:red">O(n²) 慢</span>   | <span style="color:red">O(n²) 慢</span>  | <span style="color:green">O(1) 小</span> | <span style="color:green">穩定</span> |
| Bubble Sort (泡沫排序)       | <span style="color:green">O(n) 快</span> | <span style="color:red">O(n²) 慢</span>   | <span style="color:red">O(n²) 慢</span>  | <span style="color:green">O(1) 小</span> | <span style="color:green">穩定</span> |
| Quick Sort (快速排序)        | <span style="color:orange">O(n log n) 中快</span> | <span style="color:orange">O(n log n) 中快</span> | <span style="color:red">O(n²) 慢</span>  | <span style="color:green">O(log n) 小</span> | <span style="color:red">不穩定</span> |
| Merge Sort (合併排序)        | <span style="color:orange">O(n log n) 中快</span> | <span style="color:orange">O(n log n) 中快</span> | <span style="color:orange">O(n log n) 中快</span> | <span style="color:orange">O(n) 中</span> | <span style="color:green">穩定</span> |


### 速記筆記
Quick Sort & Merge Sort 面試速記版

---

Quick Sort (快速排序) 

**原理口訣**：選 pivot → 小的放左、大的放右 → 左右各自遞迴  

- **核心步驟**：
  1. 選一個 pivot（通常最後一個元素）  
  2. partition：把小於 pivot 的數放左邊，大於的放右邊  
  3. 遞迴呼叫左半部、右半部  

- **時間複雜度**：
  - 最佳 O(n log n) ⚡
  - 平均 O(n log n) ⚡
  - 最壞 O(n²) 🔴（pivot 每次選到最大或最小）  

- **空間複雜度**：
  - O(log n)（遞迴 Stack）  

- **穩定性**：
  - 不穩定 ❌

- **核心程式片段**：
```c
void swap(int* a, int* b) { int t = *a; *a = *b; *b = t; }

int partition(int arr[], int low, int high) {
    int pivot = arr[high], i = low - 1;
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) { i++; swap(&arr[i], &arr[j]); }
    }
    swap(&arr[i+1], &arr[high]);
    return i+1;
}
```
> 面試時只要能描述「拆半、遞迴、合併」，再補上 merge 核心程式片段就足夠。 (當然還是希望自己能記得起來)

---
Merge Sort (合併排序) 

**原理口訣**：拆半 → 左右遞迴 → 合併小組  

**核心步驟**：
1. 將陣列拆成兩半
2. 遞迴排序左右半邊
3. merge：將兩個已排序小陣列合併

**時間複雜度**：
- 最佳 O(n log n) ⚡
- 平均 O(n log n) ⚡
- 最壞 O(n log n) ⚡

**空間複雜度**：
- O(n) ⚠️（需暫存左右陣列）

**穩定性**：
- 穩定 ✅

**核心程式片段**：
```c
void merge(int arr[], int l, int m, int r) {
    int n1 = m - l + 1, n2 = r - m;
    int L[n1], R[n2];
    for(int i = 0; i < n1; i++) L[i] = arr[l + i];
    for(int j = 0; j < n2; j++) R[j] = arr[m + 1 + j];
    int i = 0, j = 0, k = l;
    while(i < n1 && j < n2) 
        arr[k++] = (L[i] <= R[j]) ? L[i++] : R[j++];
    while(i < n1) arr[k++] = L[i++];
    while(j < n2) arr[k++] = R[j++];
}
```
> 面試時口頭描述即可：「拆半 → 左右遞迴 → 合併」，再補上 merge 核心程式片段就足夠。 (還是要盡量熟練)
