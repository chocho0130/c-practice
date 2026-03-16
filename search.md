##  搜尋(Search)

比較常考的 Search Algorithm：

1. Linear Search
2. Binary Search
3. DFS (Depth First Search)
4. BFS (Breadth First Search)
5. Hash Table Search

---

### 1. Linear Search(線性搜尋)
從陣列第一個元素開始逐一比較，直到找到目標，也是暴力法。

---
 Time Complexity

| Case | Complexity |
|-----|-----|
| Best | O(1) |
| Average | O(n) |
| Worst | O(n) |

---
```c
int linear_search(int arr[], int n, int target)
{
    for(int i = 0; i < n; i++)
    {
        if(arr[i] == target)
        {
            return i;
        }
    }
    return -1;
}
```
---

### 2. Binary Search(二元搜尋)
每次將搜尋範圍縮小一半，比較常考這個。

條件：
- 陣列必須 **已排序 (sorted)**

---
 Time Complexity
 
| Case | Complexity |
|-----|-----|
| Best | O(1) |
| Average | O(log n) |
| Worst | O(log n) |

---

```c
int binary_search(int arr[], int left, int right, int target)
{
    while(left <= right)
    {
        int mid = left + (right - left) / 2;  //避免overflow盡量不要寫(left + right) / 2

        if(arr[mid] == target)
            return mid;

        if(arr[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }

    return -1;
}
```
---
Binary Search 常見五題：

First occurrence → 第一個出現

Last occurrence → 最後一個出現

Lower bound → 第一個 ≥ target

Upper bound → 第一個 > target

Search in rotated array → 旋轉排序陣列搜尋

---

### 3. DFS (Depth First Search)
中文為深度優先搜尋
先走到最深，再回頭找其他路徑。

---
常用於：
- Tree traversal
- Graph traversal
  
Tree traversal 常見順序：

- Preorder
- Inorder
- Postorder
  
---

Time Complexity

O(V + E)
> V = number of vertices  
> E = number of edges  

---

```c
#include <stdio.h>

#define MAX 100

int visited[MAX];
int graph[MAX][MAX];

void dfs(int v, int n){
    visited[v] = 1;
    printf("%d ", v);

    for(int i = 0; i < n; i++){
        if(graph[v][i] && !visited[i]){
            dfs(i, n);
        }
    }
}

```
---

### 4. BFS (Breadth First Search)
中文為廣度優先搜尋
一層一層搜尋節點。

需要資料結構：
Queue

---

Time Complexity

O(V + E)

---

```c
#include <stdio.h>

#define MAX 100

int queue[MAX];
int front = 0;
int rear = 0;

int visited[MAX];
int graph[MAX][MAX];

void bfs(int start, int n)
{
    queue[rear++] = start;
    visited[start] = 1;

    while(front < rear){
        int v = queue[front++];
        printf("%d ", v);

        for(int i = 0; i < n; i++){
            if(graph[v][i] && !visited[i]){
                queue[rear++] = i;
                visited[i] = 1;
            }
        }
    }
}

```
---

### 5. Hash Table Search
透過 Hash Function 直接找到資料位置。
```c
index = hash(key)
```
平均情況下查找速度非常快。

---

 Time Complexity
 
| Case | Complexity |
|-----|-----|
| Average | O(1) |
| Worst | O(n) |



Worst case 可能發生在：
- hash collision

--- 

```c
#include <stdio.h>

#define SIZE 10

int hash_table[SIZE];

int hash(int key){
    return key % SIZE;
}

void insert(int key){
    int index = hash(key);
    hash_table[index] = key;
}

int search(int key){
    int index = hash(key);

    if(hash_table[index] == key)
        return index;

    return -1;
}

```
> 簡化版本，未處理 collision

---

###  Quick Summary

| Algorithm       | Requirement   | Complexity    |
|-----------------|--------------|--------------|
| Linear Search   | None         | O(n)         |
| Binary Search   | Sorted Array | O(log n)     |
| DFS             | Graph / Tree | O(V + E)     |
| BFS             | Graph / Tree | O(V + E)     |
| Hash Search     | Hash Table   | O(1) average |

---

### 6. Two Pointer Search (雙指標搜尋)

使用兩個指標從不同方向移動，常用於 **排序陣列或字串問題**。

常見用途：

- Two Sum (sorted array)
- 移除重複元素
- 反轉字串
- 判斷回文 (Palindrome)

---

Time Complexity

O(n)

---

```c
// 找到兩個數字相加等於 target (sorted array)

int two_sum(int arr[], int n, int target)
{
    int left = 0;
    int right = n - 1;

    while(left < right)
    {
        int sum = arr[left] + arr[right];

        if(sum == target)
        {
            return 1;
        }
        else if(sum < target)
        {
            left++;
        }
        else
        {
            right--;
        }
    }

    return 0;
}
```
---

### 7. Sliding Window (滑動視窗)
維持一個 區間(window)，逐步向右移動。

適合問題：

- Subarray sum 
- Longest substring 
- Maximum sum subarray

---

Time Complexity  
O(n)

---

```c
// 找最大長度子陣列總和 

int max_subarray_sum(int arr[], int n, int k) //給一個 array，找出 長度為 k 的子陣列最大總和 k=子序列長度 n為陣列長度
{
    int window_sum = 0; 

    // 計算第一個 window
    for(int i = 0; i < k; i++)
    {
        window_sum += arr[i];
    }

    int max_sum = window_sum;

    // 開始滑動 window
    for(int i = k; i < n; i++)
    {
        window_sum += arr[i];     // 新元素加入
        window_sum -= arr[i-k];   // 舊元素移出

        if(window_sum > max_sum)
        {
            max_sum = window_sum;
        }
    }

    return max_sum;
}

```
--- 

### 8. Backtracking (回溯搜尋)
透過 嘗試所有可能解，如果不符合條件就回退。

常用於：

- Permutation

- Combination

- N-Queens

- Sudoku

本質上是一種 DFS 搜尋策略。

---
Time Complexity  
通常為:
O(2^n) 或 O(n!)

---

```c
#include <stdio.h>

void permutation(int arr[], int start, int n)
{
    if(start == n)
    {
        for(int i = 0; i < n; i++)
            printf("%d ", arr[i]);
        printf("\n");
        return;
    }

    for(int i = start; i < n; i++)
    {
        int temp = arr[start];
        arr[start] = arr[i];
        arr[i] = temp;

        permutation(arr, start + 1, n);

        temp = arr[start];
        arr[start] = arr[i];
        arr[i] = temp;
    }
}
```
---

### Interview Quick Summary

| Algorithm | Requirement | Time Complexity |
|----------|-------------|----------------|
| Linear Search | None | O(n) |
| Binary Search | Sorted Array | O(log n) |
| DFS | Graph / Tree | O(V + E) |
| BFS | Graph / Tree | O(V + E) |
| Hash Table Search | Hash Table | O(1) average |
| Two Pointer | Sorted Array / String | O(n) |
| Sliding Window | Subarray / Substring | O(n) |
| Backtracking | Combinational Search | O(2^n) |
