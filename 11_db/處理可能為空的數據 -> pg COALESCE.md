
`COALESCE`  處理可能為空的數據時，且需要提供替代值的情況下

它用來返回其參數中第一個非空（non-null）的值。這意味著如果你有多個可能為空的值，`COALESCE` 會檢查這些值，並返回第一個不為空的值。


### 語法

COALESCE(value1, value2, ..., valueN)`

### 例子

假設我們有一個名為 `employees` 的表格，裡面有以下欄位：

- `id` (員工編號)
- `name` (員工姓名)
- `email` (電子郵件)
- `phone` (電話)

表格的數據如下：

|id|name|email|phone|
|---|---|---|---|
|1|Alice|alice@example.com|NULL|
|2|Bob|NULL|123-456-7890|
|3|Charlie|NULL|NULL|

如果我們想要查詢每位員工的聯絡方式，並希望在電子郵件為空的情況下使用電話號碼，我們可以使用 `COALESCE` 函數：

```sql

SELECT 
    id, 
    name, 
    COALESCE(email, phone) AS contact_info
FROM 
    employees;

```


### 查詢結果

這個查詢的結果將會是：

|id|name|contact_info|
|---|---|---|
|1|Alice|alice@example.com|
|2|Bob|123-456-7890|
|3|Charlie|NULL|

在這個例子中：

- 對於 Alice，`COALESCE` 返回了她的電子郵件 `alice@example.com`，因為它不是空的。
- 對於 Bob，因為他的電子郵件是空的，所以 `COALESCE` 返回了他的電話號碼 `123-456-7890`。
- 對於 Charlie，因為他的電子郵件和電話號碼都是空的，所以 `COALESCE` 返回了 `NULL`。


