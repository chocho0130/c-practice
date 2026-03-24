##  Linked List (基本結構)

#### 基本結構

```c
typedef struct Node { //用typedef比較方便 
    int data;
    struct Node* next;
} Node;

```
---

#### 常見操作

1. 插入（頭插法）

```c
Node* push(Node* head, int val) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    newNode->data = val;
    newNode->next = head;
    return newNode;
}

```
---

2. 刪除（刪指定值）
```c
Node* delete(Node* head, int key) {
    Node *curr = head, *prev = NULL;

    if (curr && curr->data == key) {
        head = curr->next;
        free(curr);
        return head;
    }

    while (curr && curr->data != key) {
        prev = curr;
        curr = curr->next;
    }

    if (!curr) return head;

    prev->next = curr->next;
    free(curr);
    return head;
}

```

---

### Search（搜尋）

#### Linear Search (線性搜尋)

#### 主要作法
就重頭找linked list 
```c
Node* search(Node* head, int target) {
    Node* curr = head;
    while (curr) {
        if (curr->data == target) return curr;
        curr = curr->next;
    }
    return NULL;
}
```
---

### Sort（排序）

#### Merge Sort (合併排序) 
這是最常考的

#### 主要作法
拆半 → 排序 → 合併

1. 找中點（快慢指標）
```
Node* getMiddle(Node* head) {
    Node *slow = head, *fast = head->next;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    return slow;
}
```
2. Merge 兩個 sorted list

```
Node* merge(Node* a, Node* b) {
    if (!a) return b;
    if (!b) return a;

    Node* result = NULL;

    if (a->data <= b->data) {
        result = a;
        result->next = merge(a->next, b);
    } else {
        result = b;
        result->next = merge(a, b->next);
    }

    return result;
}
```
3. Merge Sort 主函式

```c
Node* mergeSort(Node* head) {
    if (!head || !head->next) return head;

    Node* mid = getMiddle(head);
    Node* right = mid->next;
    mid->next = NULL;

    Node* left = mergeSort(head);
    right = mergeSort(right);

    return merge(left, right);
}
```
---

### 常考考題

### Reverse Linked List（反轉鏈結串列）

#### 原理口訣
prev ← curr ← next  
👉 一個一個反轉指向

#### 作法（Iterative）
```c
Node* reverse(Node* head) {
    Node *prev = NULL, *curr = head, *next = NULL;

    while (curr) {
        next = curr->next;   // 暫存下一個
        curr->next = prev;   // 反轉指向
        prev = curr;         // prev 往前移
        curr = next;         // curr 往前移
    }

    return prev;
}

```
---

### Cycle Detection（是否有環）


#### 原理（Floyd 快慢指標）

- slow 一次走 1 步  
- fast 一次走 2 步  
👉 如果有環，一定會相遇

#### 作法
```c
bool hasCycle(Node *head) {
    Node *slow = head, *fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;

        if (slow == fast)
            return true;
    }
    return false;
}
```
---
### LeetCode 考題

> 707. Design Linked List https://leetcode.com/problems/design-linked-list/description/
主要實現linkedlist各種功能
> 21. Merge Two Sorted Lists https://leetcode.com/problems/merge-two-sorted-lists/description/
合併兩個已經排序的list 
