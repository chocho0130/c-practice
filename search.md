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
        int mid = left + (right - left) / 2; 

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

### 3. DFS (Depth First Search)
中文為深度優先搜尋
先走到最深，再回頭找其他路徑。

---
常用於：
- Tree traversal
- Graph traversal
  
常見順序：

- Preorder
- Inorder
- Postorder
  
---

Time Complexity

O(V + E)
> V = vertex
> E = edge

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

---

###  Quick Summary

| Algorithm       | Requirement   | Complexity    |
|-----------------|--------------|--------------|
| Linear Search   | None         | O(n)         |
| Binary Search   | Sorted Array | O(log n)     |
| DFS             | Graph / Tree | O(V + E)     |
| BFS             | Graph / Tree | O(V + E)     |
| Hash Search     | Hash Table   | O(1) average |
