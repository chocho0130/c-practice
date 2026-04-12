#SQL 語法

這邊主要是SQL基本語法，方便SQL上手

1. 查詢資料：SELECT (最常用)

這是所有 SQL 的基礎。

- 全部選取：
```SQL
SELECT * FROM Users

```

> Users 是表格名 \
口訣：從哪裡 (FROM) 拿什麼 (SELECT)。

- 只選特定欄位
```SQL
SELECT UserName, UserEmail FROM Users

```
> UserName, UserEmail 這是欄位名稱

- 條件篩選 (WHERE)：
```SQL
SELECT * FROM Users WHERE Id = 1

```
- 模糊搜尋 (LIKE)：
% 代表萬用字元（任何字）
   
```SQL
SELECT * FROM Users WHERE UserName LIKE '小%' 
```
> 找姓「小」的人
  - 用法：
    - '小%'：開頭是「小」（小明、小華）。
    - '%小'：結尾是「小」（張小、王小）。
    - '%小%'：只要裡面有「小」都算。

2. 新增資料：INSERT 
> 字串資料要用 單引號 ' ' 包起來。

```SQL
INSERT INTO Users (Id, UserName, UserEmail) 
VALUES (3, '老王', 'wang@test.com')
```
> 欄位的順序必須跟後面 Values 的順序一模一樣 \
> 塞進 (INTO) 某個表，給它這些值 (VALUES)。

3. 修改資料：UPDATE (最危險)
> 絕對、一定要加 WHERE，否則整張表都會被改掉！
```SQL
UPDATE Users 
SET UserName = '新名字', UserEmail = 'new@test.com' 
WHERE Id = 3

```
> 口訣：更新 (UPDATE) 某表，設定 (SET) 資料。

4. 刪除資料：DELETE
> 同樣的，沒加 WHERE 就會清空整張表。
```SQL
DELETE FROM Users WHERE Id = 3

```

5. 進階：關聯查詢 JOIN(兩個表)
現實中資料是分開的，不會用一個表打天下。例如：一張是「員工表」，一張是「部門表」。
```SQL
SELECT A.UserName, B.DeptName
FROM Users A
INNER JOIN Department B ON A.DeptId = B.Id

```
> 白話解釋：幫我把 Users 表 (A) 跟 Department 表 (B) 串起來，條件是 A 的部門 ID 要等於 B 的 ID。

# 一些容易混淆的觀念
可以把這張表記在腦袋裡，就不會亂掉：
<img width="789" height="343" alt="image" src="https://github.com/user-attachments/assets/9d056b0d-8791-49d1-9d37-7c483dcd09d1" />
