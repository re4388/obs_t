## SQL 基礎

### [#](https://xiaolincoding.com/interview/mysql.html#nosql%E5%92%8Csql%E7%9A%84%E5%8C%BA%E5%88%AB)NOSQL 和 SQL 的區别？

SQL 數據庫，指關系型數據庫 - 主要代表：SQL Server，Oracle，MySQL (開源)，PostgreSQL (開源)。

關系型數據庫存儲結構化數據。這些數據邏輯上以行列二維表的形式存在，每一列代表數據的一種屬性，每一行代表一個數據實體。

![[100_attachements/2516d55ffdea2636cfbd0005e92da6f6_MD5.webp]]

NoSQL 指非關系型數據庫 ，主要代表：MongoDB，Redis。NoSQL 數據庫邏輯上提供了不同於二維表的存儲方式，存儲方式可以是 JSON 文檔、哈希表或者其他方式。

![[100_attachements/4badc9e5d367a76d4e9b9c5c7496ed56_MD5.webp]]

選擇 SQL vs NoSQL，考慮以下因素。

> ACID vs BASE

關系型數據庫支持 ACID 即原子性，一致性，隔離性和持續性。相對而言，NoSQL 采用更寬松的模型 BASE ， 即基本可用，軟狀态和最終一致性。

從實用的角度出發，我們需要考慮對於面對的應用場景，ACID 是否是必須的。比如銀行應用就必須保證 ACID，否則一筆錢可能被使用兩次；又比如社交軟件不必保證 ACID，因為一條狀态的更新對於所有用戶讀取先後時間有數秒不同并不影響使用。

對於需要保證 ACID 的應用，我們可以優先考慮 SQL。反之則可以優先考慮 NoSQL。

> 擴展性對比

NoSQL 數據之間無關系，這樣就非常容易擴展，也無形之間，在架構的層面上帶來了可擴展的能力。比如 redis 自帶主從複制模式、哨兵模式、切片集群模式。

相反關系型數據庫的數據之間存在關聯性，水平擴展較難 ，需要解決跨服務器 JOIN，分布式事務等問題。

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%89%E5%A4%A7%E8%8C%83%E5%BC%8F%E6%98%AF%E4%BB%80%E4%B9%88)數據庫三大範式是什麽？

**第一範式（1NF）：要求數據庫表的每一列都是不可分割的原子數據項。**

舉例說明：

![[100_attachements/c2174abf35ee4acd8a87b6dc2cd62229_MD5.webp]]

在上面的表中，“家庭信息” 和 “學校信息” 列均不滿足原子性的要求，故不滿足第一範式，調整如下：

![[100_attachements/b3af22c197393afa11be98aabd1c70cc_MD5.webp]]

可見，調整後的每一列都是不可再分的，因此滿足第一範式（1NF）；

**第二範式（2NF）：在 1NF 的基礎上，非碼屬性必須完全依賴於候選碼（在 1NF 基礎上消除非主屬性對主碼的部分函數依賴）**

**第二範式需要确保數據庫表中的每一列都和主鍵相關，而不能隻與主鍵的某一部分相關（主要針對聯合主鍵而言）。**

舉例說明：
![[Pasted image 20250116161122.png]]

在上圖所示的情況中，同一個訂單中可能包含不同的産品，因此主鍵必須是 “訂單號” 和 “産品號” 聯合組成，

但可以發現，産品數量、産品折扣、産品價格與 “訂單號” 和 “産品號” 都相關，但是訂單金額和訂單時間僅與 “訂單號” 相關，與 “産品號” 無關，

這樣就不滿足第二範式的要求，調整如下，需分成兩個表：

![[Pasted image 20250116161132.png]]

![[100_attachements/c54243e23dcd57eb08b2dd31477a2165_MD5.png]]

**第三範式（3NF）：在 2NF 基礎上，任何非主[屬性 (opens new window)](https://baike.baidu.com/item/%E5%B1%9E%E6%80%A7)不依賴於其它非主屬性（在 2NF 基礎上消除傳遞依賴）**

**第三範式需要确保數據表中的每一列數據都和主鍵直接相關，而不能間接相關。**

舉例說明：

![[Pasted image 20250116161140.png]]

上表中，所有屬性都完全依賴於學號，所以滿足第二範式，但是 “班主任性别” 和 “班主任年齡” 直接依賴的是 “班主任姓名”，

而不是主鍵 “學號”，所以需做如下調整：

![[Pasted image 20250116161146.png]]

![[Pasted image 20250116161154.png]]

這樣以來，就滿足了第三範式的要求。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql-%E6%80%8E%E4%B9%88%E8%BF%9E%E8%A1%A8%E6%9F%A5%E8%AF%A2)MySQL 怎麽連表查詢？

數據庫有以下幾種聯表查詢類型：

1. **内連接 (INNER JOIN)**
2. **左外連接 (LEFT JOIN)**
3. **右外連接 (RIGHT JOIN)**
4. **全外連接 (FULL JOIN)**

![[Pasted image 20250116161203.png]]

**1. 内連接 (INNER JOIN)**

内連接返回兩個表中有匹配關系的行。**示例**:

```
SELECT employees.name, departments.name
FROM employees
INNER JOIN departments
ON employees.department_id = departments.id;
```

這個查詢返回每個員工及其所在的部門名稱。

**2. 左外連接 (LEFT JOIN)**

左外連接返回左表中的所有行，即使在右表中沒有匹配的行。未匹配的右表列會包含 NULL。**示例**:

```
SELECT employees.name, departments.name
FROM employees
LEFT JOIN departments
ON employees.department_id = departments.id;
```

這個查詢返回所有員工及其部門名稱，包括那些沒有分配部門的員工。

**3. 右外連接 (RIGHT JOIN)**

右外連接返回右表中的所有行，即使左表中沒有匹配的行。未匹配的左表列會包含 NULL。**示例**:

```
SELECT employees.name, departments.name
FROM employees
RIGHT JOIN departments
ON employees.department_id = departments.id;
```

這個查詢返回所有部門及其員工，包括那些沒有分配員工的部門。

**4. 全外連接 (FULL JOIN)**

全外連接返回兩個表中所有行，包括非匹配行，在 MySQL 中，FULL JOIN 需要使用 UNION 來實現，因為 MySQL 不直接支持 FULL JOIN。**示例**:

```
SELECT employees.name, departments.name
FROM employees
LEFT JOIN departments
ON employees.department_id = departments.id

UNION

SELECT employees.name, departments.name
FROM employees
RIGHT JOIN departments
ON employees.department_id = departments.id;
```

這個查詢返回所有員工和所有部門，包括沒有匹配行的記錄。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E5%A6%82%E4%BD%95%E9%81%BF%E5%85%8D%E9%87%8D%E5%A4%8D%E6%8F%92%E5%85%A5%E6%95%B0%E6%8D%AE)MySQL 如何避免重複插入數據？

**方式一：使用 UNIQUE 約束**

在表的相關列上添加 UNIQUE 約束，确保每個值在該列中唯一。例如：

```
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE,
    name VARCHAR(255)
);
```

如果嘗試插入重複的 email，MySQL 會返回錯誤。

**方式二：使用 INSERT ... ON DUPLICATE KEY UPDATE**

這種語句允許在插入記錄時處理重複鍵的情況。如果插入的記錄與現有記錄沖突，可以選擇更新現有記錄：

```
INSERT INTO users (email, name) 
VALUES ('example@example.com', 'John Doe')
ON DUPLICATE KEY UPDATE name = VALUES(name);
```

**方式三：使用 INSERT IGNORE**： 該語句會在插入記錄時忽略那些因重複鍵而導致的插入錯誤。例如：

```
INSERT IGNORE INTO users (email, name) 
VALUES ('example@example.com', 'John Doe');
```

如果 email 已經存在，這條插入語句將被忽略而不會返回錯誤。

選擇哪種方法取決於具體的需求：

- 如果需要保證全局唯一性，使用 UNIQUE 約束是最佳做法。
- 如果需要插入和更新結合可以使用 `ON DUPLICATE KEY UPDATE`。
- 對於快速忽略重複插入，`INSERT IGNORE` 是合适的選擇。

### [#](https://xiaolincoding.com/interview/mysql.html#char-%E5%92%8C-varchar%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)CHAR 和 VARCHAR 有什麽區别？

- CHAR 是固定長度的字符串類型，定義時需要指定固定長度，存儲時會在末尾補足空格。CHAR 适合存儲長度固定的數據，如固定長度的代碼、狀态等，存儲空間固定，對於短字符串效率較高。
- VARCHAR 是可變長度的字符串類型，定義時需要指定最大長度，實際存儲時根據實際長度占用存儲空間。VARCHAR 适合存儲長度可變的數據，如用戶輸入的文本、備注等，節約存儲空間。

### [#](https://xiaolincoding.com/interview/mysql.html#text%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E5%8F%AF%E4%BB%A5%E6%97%A0%E9%99%90%E5%A4%A7%E5%90%97)Text 數據類型可以無限大嗎？

MySQL 3 種 text 類型的最大長度如下：

- TEXT：65,535 bytes ~64kb
- MEDIUMTEXT：16,777,215 bytes ~16Mb
- LONGTEXT：4,294,967,295 bytes ~4Gb

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%AF%B4%E4%B8%80%E4%B8%8B%E5%A4%96%E9%94%AE%E7%BA%A6%E6%9D%9F)說一下外鍵約束

外鍵約束的作用是維護表與表之間的關系，确保數據的完整性和一致性。讓我們舉一個簡單的例子：

假設你有兩個表，一個是學生表，另一個是課程表，這兩個表之間有一個關系，即一個學生可以選修多門課程，而一門課程也可以被多個學生選修。在這種情況下，我們可以在學生表中定義一個指向課程表的外鍵，如下所示：

```
CREATE TABLE students (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  course_id INT,
  FOREIGN KEY (course_id) REFERENCES courses(id)
);
```

這裏，`students` 表中的 `course_id` 字段是一個外鍵，它指向 `courses` 表中的 `id` 字段。這個外鍵約束确保了每個學生所選的課程在 `courses` 表中都存在，從而維護了數據的完整性和一致性。

如果沒有定義外鍵約束，那麽就有可能出現學生選了不存在的課程或者删除了一個課程而忘記從學生表中删除選修該課程的學生的情況，這會破壞數據的完整性和一致性。因此，使用外鍵約束可以幫助我們避免這些問題。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E7%9A%84%E5%85%B3%E9%94%AE%E5%AD%97in%E5%92%8Cexist)MySQL 的關鍵字 in 和 exist

在 MySQL 中，`IN` 和 `EXISTS` 都是用來處理子查詢的關鍵詞，但它們在功能、性能和使用場景上有各自的特點和區别。

> IN 關鍵字

`IN` 用於檢查左邊的表達式是否存在於右邊的列表或子查詢的結果集中。如果存在，則 `IN` 返回 `TRUE`，否則返回 `FALSE`。

語法結構：

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

或

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT column_name FROM another_table WHERE condition);
```

例子：

```
SELECT * FROM Customers
WHERE Country IN ('Germany', 'France');
```

> EXISTS 關鍵字

`EXISTS` 用於判斷子查詢是否至少能返回一行數據。它不關心子查詢返回什麽數據，隻關心是否有結果。如果子查詢有結果，則 `EXISTS` 返回 `TRUE`，否則返回 `FALSE`。

語法結構：

```
SELECT column_name(s)
FROM table_name
WHERE EXISTS (SELECT column_name FROM another_table WHERE condition);
```

例子：

```
SELECT * FROM Customers
WHERE EXISTS (SELECT 1 FROM Orders WHERE Orders.CustomerID = Customers.CustomerID);
```

區别與選擇：

- **性能差異**：在很多情況下，`EXISTS` 的性能優於 `IN`，特别是當子查詢的表很大時。這是因為 `EXISTS` 一旦找到匹配項就會立即停止查詢，而 `IN` 可能會掃描整個子查詢結果集。
- **使用場景**：如果子查詢結果集較小且不頻繁變動，`IN` 可能更直觀易懂。而當子查詢涉及外部查詢的每一行判斷，并且子查詢的效率較高時，`EXISTS` 更為合适。
- **NULL 值處理**：`IN` 能夠正确處理子查詢中包含 NULL 值的情況，而 `EXISTS` 不受子查詢結果中 NULL 值的影響，因為它關注的是行的存在性，而不是具體值。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E4%B8%AD%E7%9A%84%E4%B8%80%E4%BA%9B%E5%9F%BA%E6%9C%AC%E5%87%BD%E6%95%B0-%E4%BD%A0%E7%9F%A5%E9%81%93%E5%93%AA%E4%BA%9B)mysql 中的一些基本函數，你知道哪些？

> 一、字符串函數

**CONCAT(str1, str2, ...)**：連接多個字符串，返回一個合并後的字符串。

```
SELECT CONCAT('Hello', ' ', 'World') AS Greeting;
```

**LENGTH(str)**：返回字符串的長度（字符數）。

```
SELECT LENGTH('Hello') AS StringLength;
```

**SUBSTRING(str, pos, len)**：從指定位置開始，截取指定長度的子字符串。

```
SELECT SUBSTRING('Hello World', 1, 5) AS SubStr;
```

**REPLACE(str, from_str, to_str)**：將字符串中的某部分替換為另一個字符串。

```
SELECT REPLACE('Hello World', 'World', 'MySQL') AS ReplacedStr;
```

> 二、數值函數

**ABS(num)**：返回數字的絕對值。

```
SELECT ABS(-10) AS AbsoluteValue;
```

**POWER(num, exponent)**：返回指定數字的指定幂次方。

```
SELECT POWER(2, 3) AS PowerValue;
```

> 三、日期和時間函數

**NOW()**：返回當前日期和時間。

```
SELECT NOW() AS CurrentDateTime;
```

**CURDATE()**：返回當前日期。

```
SELECT CURDATE() AS CurrentDate;
```

> 四、聚合函數

**COUNT(column)**：計算指定列中的非 NULL 值的個數。

```
SELECT COUNT(*) AS RowCount FROM my_table;
```

**SUM(column)**：計算指定列的總和。

```
SELECT SUM(price) AS TotalPrice FROM orders;
```

**AVG(column)**：計算指定列的平均值。

```
SELECT AVG(price) AS AveragePrice FROM orders;
```

**MAX(column)**：返回指定列的最大值。

```
SELECT MAX(price) AS MaxPrice FROM orders;
```

**MIN(column)**：返回指定列的最小值。

```
SELECT MIN(price) AS MinPrice FROM orders;
```

### [#](https://xiaolincoding.com/interview/mysql.html#sql%E6%9F%A5%E8%AF%A2%E8%AF%AD%E5%8F%A5%E7%9A%84%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F%E6%98%AF%E6%80%8E%E4%B9%88%E6%A0%B7%E7%9A%84)**SQL 查詢語句的執行順序是怎麽樣的？**

![[100_attachements/2b4646d8845d0664b416cc01a3970a1a_MD5.png]]

所有的查詢語句都是從 FROM 開始執行，在執行過程中，每個步驟都會生成一個虛拟表，這個虛拟表將作為下一個執行步驟的輸入，最後一個步驟産生的虛拟表即為輸出結果。

```
(9) SELECT 
(10) DISTINCT <column>,
(6) AGG_FUNC <column> or <expression>, ...
(1) FROM <left_table> 
    (3) <join_type>JOIN<right_table>
    (2) ON<join_condition>
(4) WHERE <where_condition>
(5) GROUP BY <group_by_list>
(7) WITH {CUBE|ROLLUP}
(8) HAVING <having_condtion>
(11) ORDER BY <order_by_list>
(12) LIMIT <limit_number>;
```

### [#](https://xiaolincoding.com/interview/mysql.html#sql%E9%A2%98-%E7%BB%99%E5%AD%A6%E7%94%9F%E8%A1%A8%E3%80%81%E8%AF%BE%E7%A8%8B%E6%88%90%E7%BB%A9%E8%A1%A8-%E6%B1%82%E4%B8%8D%E5%AD%98%E5%9C%A801%E8%AF%BE%E7%A8%8B%E4%BD%86%E5%AD%98%E5%9C%A802%E8%AF%BE%E7%A8%8B%E7%9A%84%E5%AD%A6%E7%94%9F%E7%9A%84%E6%88%90%E7%BB%A9)sql 題：給學生表、課程成績表，求不存在 01 課程但存在 02 課程的學生的成績

可以使用 SQL 的子查詢和 `LEFT JOIN` 或者 `EXISTS` 關鍵字來實現，這裏我將展示兩種不同的方法來完成這個查詢。

假設我們有以下兩張表：

1. `Student` 表，其中包含學生的 `sid`（學生編號）和其他相關信息。
2. `Score` 表，其中包含 `sid`（學生編號），`cid`（課程編號）和 `score`（分數）。

方法 1：使用 LEFT JOIN 和 IS NULL

```
SELECT s.sid, s.sname, sc2.cid, sc2.score
FROM Student s
LEFT JOIN Score AS sc1 ON s.sid = sc1.sid AND sc1.cid = '01'
LEFT JOIN Score AS sc2 ON s.sid = sc2.sid AND sc2.cid = '02'
WHERE sc1.cid IS NULL AND sc2.cid IS NOT NULL;
```

方法 2：使用 NOT EXISTS

```
SELECT s.sid, s.sname, sc.cid, sc.score
FROM Student s
JOIN Score sc ON s.sid = sc.sid AND sc.cid = '02'
WHERE NOT EXISTS (
    SELECT 1 FROM Score sc1 WHERE sc1.sid = s.sid AND sc1.cid = '01'
);
```

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%BB%99%E5%AE%9A%E4%B8%80%E4%B8%AA%E5%AD%A6%E7%94%9F%E8%A1%A8-student-score-stu-id-subject-id-score-%E6%9F%A5%E8%AF%A2%E6%80%BB%E5%88%86%E6%8E%92%E5%90%8D%E5%9C%A85-10%E5%90%8D%E7%9A%84%E5%AD%A6%E7%94%9Fid%E5%8F%8A%E5%AF%B9%E5%BA%94%E7%9A%84%E6%80%BB%E5%88%86)給定一個學生表 student_score（stu_id，subject_id，score），查詢總分排名在 5-10 名的學生 id 及對應的總分

可以使用以下 SQL 查詢來檢索總分排名在 5 到 10 名的學生 ID 及對應的總分。其中我們先計算每個學生的總分，然後為其分配一個排名，最後檢索排名在 5 到 10 之間的記錄。

```
WITH StudentTotalScores AS (
    SELECT 
        stu_id,
        SUM(score) AS total_score
    FROM 
        student_score
    GROUP BY 
        stu_id
),
RankedStudents AS (
    SELECT
        stu_id,
        total_score,
        RANK() OVER (ORDER BY total_score DESC) AS ranking
    FROM
        StudentTotalScores
)
SELECT
    stu_id,
    total_score
FROM
    RankedStudents
WHERE
    ranking BETWEEN 5 AND 10;
```

解釋：

1. 子查詢 StudentTotalScores 中，我們通過對 student_score 表中的 stu_id 分組來計算每個學生的總分。
2. 子查詢 RankedStudents 中，我們使用 RANK () 函數為每個學生分配一個排名，按總分從高到低排序。
3. 最後，我們在主查詢中選擇排名在 5 到 10 之間的學生。

## [#](https://xiaolincoding.com/interview/mysql.html#%E5%AD%98%E5%82%A8%E5%BC%95%E6%93%8E)存儲引擎

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%89%A7%E8%A1%8C%E4%B8%80%E6%9D%A1sql%E8%AF%B7%E6%B1%82%E7%9A%84%E8%BF%87%E7%A8%8B%E6%98%AF%E4%BB%80%E4%B9%88)執行一條 SQL 請求的過程是什麽？

先來一個上帝視角圖，下面就是 MySQL 執行一條 SQL 查詢語句的流程，也從圖中可以看到 MySQL 内部架構裏的各個功能模塊。

![[Pasted image 20250116161227.png]]

- 連接器：建立連接，管理連接、校驗用戶身份；
    
- 查詢緩存：查詢語句如果命中查詢緩存則直接返回，否則繼續往下執行。MySQL 8.0 已删除該模塊；
    
- 解析 SQL，通過解析器對 SQL 查詢語句進行詞法分析、語法分析，然後構建語法樹，方便後續模塊讀取表名、字段、語句類型；
    
- 執行 SQL：執行 SQL 共有三個階段：
    
    - 預處理階段：檢查表或字段是否存在；將 `select *` 中的 `*` 符號擴展為表上的所有列。
        
    - 優化階段：基於查詢成本的考慮， 選擇查詢成本最小的執行計劃；
        
    - 執行階段：根據執行計劃執行 SQL 查詢語句，從存儲引擎讀取記錄，返回給客戶端；
        

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%AE%B2%E4%B8%80%E8%AE%B2mysql%E7%9A%84%E5%BC%95%E6%93%8E%E5%90%A7-%E4%BD%A0%E6%9C%89%E4%BB%80%E4%B9%88%E4%BA%86%E8%A7%A3)講一講 mysql 的引擎吧，你有什麽了解？

- InnoDB：InnoDB 是 MySQL 的默認存儲引擎，具有 ACID 事務支持、行級鎖、外鍵約束等特性。它适用於高并發的讀寫操作，支持較好的數據完整性和并發控制。
- MyISAM：MyISAM 是 MySQL 的另一種常見的存儲引擎，具有較低的存儲空間和内存消耗，适用於大量讀操作的場景。然而，MyISAM 不支持事務、行級鎖和外鍵約束，因此在并發寫入和數據完整性方面有一定的限制。
- Memory：Memory 引擎將數據存儲在内存中，适用於對性能要求較高的讀操作，但是在服務器重啓或崩潰時數據會丢失。它不支持事務、行級鎖和外鍵約束。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E4%B8%BA%E4%BB%80%E4%B9%88innodb%E6%98%AF%E9%BB%98%E8%AE%A4%E5%BC%95%E6%93%8E)MySQL 為什麽 InnoDB 是默認引擎？

InnoDB 引擎在事務支持、并發性能、崩潰恢複等方面具有優勢，因此被 MySQL 選擇為默認的存儲引擎。

- 事務支持：InnoDB 引擎提供了對事務的支持，可以進行 ACID（原子性、一致性、隔離性、持久性）屬性的操作。Myisam 存儲引擎是不支持事務的。
- 并發性能：InnoDB 引擎采用了行級鎖定的機制，可以提供更好的并發性能，Myisam 存儲引擎隻支持表鎖，鎖的粒度比較大。
- 崩潰恢複：InnoDB 引引擎通過 redolog 日志實現了崩潰恢複，可以在數據庫發生異常情況（如斷電）時，通過日志文件進行恢複，保證數據的持久性和一致性。Myisam 是不支持崩潰恢複的。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%AF%B4%E4%B8%80%E4%B8%8Bmysql%E7%9A%84innodb%E4%B8%8Emyisam%E7%9A%84%E5%8C%BA%E5%88%AB)說一下 mysql 的 innodb 與 MyISAM 的區别？

- **事務**：InnoDB 支持事務，MyISAM 不支持事務，這是 MySQL 將默認存儲引擎從 MyISAM 變成 InnoDB 的重要原因之一。
- **索引結構**：InnoDB 是聚簇索引，MyISAM 是非聚簇索引。聚簇索引的文件存放在主鍵索引的葉子節點上，因此 InnoDB 必須要有主鍵，通過主鍵索引效率很高。但是輔助索引需要兩次查詢，先查詢到主鍵，然後再通過主鍵查詢到數據。因此，主鍵不應該過大，因為主鍵態大，其他索引也都會很大。而 MyISAM 是非聚簇索引，數據文件是分離的，索引保存的是數據文件的指針。主鍵索引和輔助索引是獨立的。
- **鎖粒度**：InnoDB 最小的鎖粒度是行鎖，MyISAM 最小的鎖粒度是表鎖。一個更新語句會鎖住整張表，導致其他查詢和更新都會被阻塞，因此并發訪問受限。
- **count 的效率**：InnoDB 不保存表的具體行數，執行 select count (*) from table 時需要全表掃描。而 MyISAM 用一個變量保存了整個表的行數，執行上述語句時隻需要讀出該變量即可，速度很快。

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%95%B0%E6%8D%AE%E7%AE%A1%E7%90%86%E9%87%8C-%E6%95%B0%E6%8D%AE%E6%96%87%E4%BB%B6%E5%A4%A7%E4%BD%93%E5%88%86%E6%88%90%E5%93%AA%E5%87%A0%E7%A7%8D%E6%95%B0%E6%8D%AE%E6%96%87%E4%BB%B6)數據管理裏，數據文件大體分成哪幾種數據文件？

我們每創建一個 database（數據庫） 都會在 /var/lib/mysql/ 目錄裏面創建一個以 database 為名的目錄，然後保存表結構和表數據的文件都會存放在這個目錄裏。

比如，我這裏有一個名為 my_test 的 database，該 database 裏有一張名為 t_order 數據庫表。
![[Pasted image 20250116161238.png]]

然後，我們進入 /var/lib/mysql/my_test 目錄，看看裏面有什麽文件？

```
[root@xiaolin ~]#ls /var/lib/mysql/my_test
db.opt  
t_order.frm  
t_order.ibd
```

可以看到，共有三個文件，這三個文件分别代表着：

- db.opt，用來存儲當前數據庫的默認字符集和字符校驗規則。
- t_order.frm ，t_order 的表結構會保存在這個文件。在 MySQL 中建立一張表都會生成一個.frm 文件，該文件是用來保存每個表的元數據信息的，主要包含表結構定義。
- t_order.ibd，t_order 的表數據會保存在這個文件。表數據既可以存在共享表空間文件（文件名：ibdata1）裏，也可以存放在獨占表空間文件（文件名：表名字.ibd）。這個行為是由參數 innodb_file_per_table 控制的，若設置了參數 innodb_file_per_table 為 1，則會將存儲的數據、索引等信息單獨存儲在一個獨占表空間，從 MySQL 5.6.6 版本開始，它的默認值就是 1 了，因此從這個版本之後， MySQL 中每一張表的數據都存放在一個獨立的 .ibd 文件。

## [#](https://xiaolincoding.com/interview/mysql.html#%E7%B4%A2%E5%BC%95)索引

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%B4%A2%E5%BC%95%E6%98%AF%E4%BB%80%E4%B9%88-%E6%9C%89%E4%BB%80%E4%B9%88%E5%A5%BD%E5%A4%84)索引是什麽？有什麽好處？

索引類似於書籍的目錄，可以減少掃描的數據量，提高查詢效率。

- 如果查詢的時候，沒有用到索引就會全表掃描，這時候查詢的時間複雜度是 On
- 如果用到了索引，那麽查詢的時候，可以基於二分查找算法，通過索引快速定位到目標數據， mysql 索引的數據結構一般是 b + 樹，其搜索複雜度為 O (logdN)，其中 d 表示節點允許的最大子節點個數為 d 個。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%AE%B2%E8%AE%B2%E7%B4%A2%E5%BC%95%E7%9A%84%E5%88%86%E7%B1%BB%E6%98%AF%E4%BB%80%E4%B9%88)講講索引的分類是什麽？

MySQL 可以按照四個角度來分類索引。

- 按「數據結構」分類：**B+tree 索引、Hash 索引、Full-text 索引**。
- 按「物理存儲」分類：**聚簇索引（主鍵索引）、二級索引（輔助索引）**。
- 按「字段特性」分類：**主鍵索引、唯一索引、普通索引、前綴索引**。
- 按「字段個數」分類：**單列索引、聯合索引**。

接下來，按照這些角度來說說各類索引的特點。

> 按數據結構分類

從數據結構的角度來看，MySQL 常見索引有 B+Tree 索引、HASH 索引、Full-Text 索引。

每一種存儲引擎支持的索引類型不一定相同，我在表中總結了 MySQL 常見的存儲引擎 InnoDB、MyISAM 和 Memory 分别支持的索引類型。
![[Pasted image 20250116161247.png]]

InnoDB 是在 MySQL 5.5 之後成為默認的 MySQL 存儲引擎，B+Tree 索引類型也是 MySQL 存儲引擎采用最多的索引類型。

在創建表時，InnoDB 存儲引擎會根據不同的場景選擇不同的列作為索引：

- 如果有主鍵，默認會使用主鍵作為聚簇索引的索引鍵（key）；
- 如果沒有主鍵，就選擇第一個不包含 NULL 值的唯一列作為聚簇索引的索引鍵（key）；
- 在上面兩個都沒有的情況下，InnoDB 將自動生成一個隐式自增 id 列作為聚簇索引的索引鍵（key）；

其它索引都屬於輔助索引（Secondary Index），也被稱為二級索引或非聚簇索引。**創建的主鍵索引和二級索引默認使用的是 B+Tree 索引**。

> 按物理存儲分類

從物理存儲的角度來看，索引分為聚簇索引（主鍵索引）、二級索引（輔助索引）。

這兩個區别在前面也提到了：

- 主鍵索引的 B+Tree 的葉子節點存放的是實際數據，所有完整的用戶記錄都存放在主鍵索引的 B+Tree 的葉子節點裏；
- 二級索引的 B+Tree 的葉子節點存放的是主鍵值，而不是實際數據。

所以，在查詢時使用了二級索引，如果查詢的數據能在二級索引裏查詢的到，那麽就不需要回表，這個過程就是覆蓋索引。如果查詢的數據不在二級索引裏，就會先檢索二級索引，找到對應的葉子節點，獲取到主鍵值後，然後再檢索主鍵索引，就能查詢到數據了，這個過程就是回表。

> 按字段特性分類

從字段特性的角度來看，索引分為主鍵索引、唯一索引、普通索引、前綴索引。

- 主鍵索引

主鍵索引就是建立在主鍵字段上的索引，通常在創建表的時候一起創建，一張表最多隻有一個主鍵索引，索引列的值不允許有空值。

在創建表時，創建主鍵索引的方式如下：

```
CREATE TABLE table_name  (
  ....
  PRIMARY KEY (index_column_1) USING BTREE
);
```

- 唯一索引

唯一索引建立在 UNIQUE 字段上的索引，一張表可以有多個唯一索引，索引列的值必須唯一，但是允許有空值。

在創建表時，創建唯一索引的方式如下：

```
CREATE TABLE table_name  (
  ....
  UNIQUE KEY(index_column_1,index_column_2,...) 
);
```

建表後，如果要創建唯一索引，可以使用這面這條命令：

```
CREATE UNIQUE INDEX index_name
ON table_name(index_column_1,index_column_2,...);
```

- 普通索引

普通索引就是建立在普通字段上的索引，既不要求字段為主鍵，也不要求字段為 UNIQUE。

在創建表時，創建普通索引的方式如下：

```
CREATE TABLE table_name  (
  ....
  INDEX(index_column_1,index_column_2,...) 
);
```

建表後，如果要創建普通索引，可以使用這面這條命令：

```
CREATE INDEX index_name
ON table_name(index_column_1,index_column_2,...);
```

- 前綴索引

前綴索引是指對字符類型字段的前幾個字符建立的索引，而不是在整個字段上建立的索引，前綴索引可以建立在字段類型為 char、 varchar、binary、varbinary 的列上。

使用前綴索引的目的是為了減少索引占用的存儲空間，提升查詢效率。

在創建表時，創建前綴索引的方式如下：

```
CREATE TABLE table_name(
    column_list,
    INDEX(column_name(length))
);
```

建表後，如果要創建前綴索引，可以使用這面這條命令：

```
CREATE INDEX index_name
ON table_name(column_name(length));
```

> 按字段個數分類

從字段個數的角度來看，索引分為單列索引、聯合索引（複合索引）。

- 建立在單列上的索引稱為單列索引，比如主鍵索引；
- 建立在多列上的索引稱為聯合索引；

通過將多個字段組合成一個索引，該索引就被稱為聯合索引。

比如，將商品表中的 product_no 和 name 字段組合成聯合索引 (product_no, name)，創建聯合索引的方式如下：

```
CREATE INDEX index_product_no_name ON product(product_no, name);
```

聯合索引 (product_no, name) 的 B+Tree 示意圖如下（圖中葉子節點之間我畫了單向鏈表，但是實際上是雙向鏈表，原圖我找不到了，修改不了，偷個懶我不重畫了，大家腦補成雙向鏈表就行）。

![[Pasted image 20250116161304.png]]

可以看到，聯合索引的非葉子節點用兩個字段的值作為 B+Tree 的 key 值。當在聯合索引查詢數據時，先按 product_no 字段比較，在 product_no 相同的情況下再按 name 字段比較。

也就是說，聯合索引查詢的 B+Tree 是先按 product_no 進行排序，然後再 product_no 相同的情況再按 name 字段排序。

因此，使用聯合索引時，存在**最左匹配原則**，也就是按照最左優先的方式進行索引的匹配。在使用聯合索引進行查詢的時候，如果不遵循「最左匹配原則」，聯合索引會失效，這樣就無法利用到索引快速查詢的特性了。

比如，如果創建了一個 (a, b, c) 聯合索引，如果查詢條件是以下這幾種，就可以匹配上聯合索引：

- where a=1；
- where a=1 and b=2 and c=3；
- where a=1 and b=2；

需要注意的是，因為有查詢優化器，所以 a 字段在 where 子句的順序并不重要。

但是，如果查詢條件是以下這幾種，因為不符合最左匹配原則，所以就無法匹配上聯合索引，聯合索引就會失效:

- where b=2；
- where c=3；
- where b=2 and c=3；

上面這些查詢條件之所以會失效，是因為 (a, b, c) 聯合索引，是先按 a 排序，在 a 相同的情況再按 b 排序，在 b 相同的情況再按 c 排序。所以，**b 和 c 是全局無序，局部相對有序的**，這樣在沒有遵循最左匹配原則的情況下，是無法利用到索引的。

聯合索引有一些特殊情況，**并不是查詢過程使用了聯合索引查詢，就代表聯合索引中的所有字段都用到了聯合索引進行索引查詢**，也就是可能存在部分字段用到聯合索引的 B+Tree，部分字段沒有用到聯合索引的 B+Tree 的情況。

這種特殊情況就發生在範圍查詢。聯合索引的最左匹配原則會一直向右匹配直到遇到「範圍查詢」就會停止匹配。**也就是範圍查詢的字段可以用到聯合索引，但是在範圍查詢字段的後面的字段無法用到聯合索引**。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95%E5%92%8C%E9%9D%9E%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)MySQL 聚簇索引和非聚簇索引的區别是什麽？
![[Pasted image 20250116161312.png]]


- **數據存儲**：在聚簇索引中，數據行按照索引鍵值的順序存儲，也就是說，索引的葉子節點包含了實際的數據行。這意味着索引結構本身就是數據的物理存儲結構。非聚簇索引的葉子節點不包含完整的數據行，而是包含指向數據行的指針或主鍵值。數據行本身存儲在聚簇索引中。
- **索引與數據關系**：由於數據與索引緊密相連，當通過聚簇索引查找數據時，可以直接從索引中獲得數據行，而不需要額外的步驟去查找數據所在的位置。當通過非聚簇索引查找數據時，首先在非聚簇索引中找到對應的主鍵值，然後通過這個主鍵值回溯到聚簇索引中查找實際的數據行，這個過程稱為 “回表”。
- **唯一性**：聚簇索引通常是基於主鍵構建的，因此每個表隻能有一個聚簇索引，因為數據隻能有一種物理排序方式。一個表可以有多個非聚簇索引，因為它們不直接影響數據的物理存儲位置。
- **效率**：對於範圍查詢和排序查詢，聚簇索引通常更有效率，因為它避免了額外的尋址開銷。非聚簇索引在使用覆蓋索引進行查詢時效率更高，因為它不需要讀取完整的數據行。但是需要進行回表的操作，使用非聚簇索引效率比較低，因為需要進行額外的回表操作。

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%A6%82%E6%9E%9C%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95%E7%9A%84%E6%95%B0%E6%8D%AE%E6%9B%B4%E6%96%B0-%E5%AE%83%E7%9A%84%E5%AD%98%E5%82%A8%E8%A6%81%E4%B8%8D%E8%A6%81%E5%8F%98%E5%8C%96)如果聚簇索引的數據更新，它的存儲要不要變化？

- 如果更新的數據是非索引數據，也就是普通的用戶記錄，那麽存儲結構是不會發生變化
- 如果更新的數據是索引數據，那麽存儲結構是有變化的，因為要維護 b + 樹的有序性

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E4%B8%BB%E9%94%AE%E6%98%AF%E8%81%9A%E7%B0%87%E7%B4%A2%E5%BC%95%E5%90%97)MySQL 主鍵是聚簇索引嗎？

在 MySQL 的 InnoDB 存儲引擎中，主鍵确實是以聚簇索引的形式存儲的。

InnoDB 將數據存儲在 B + 樹的結構中，其中主鍵索引的 B + 樹就是所謂的聚簇索引。這意味着表中的數據行在物理上是按照主鍵的順序排列的，聚簇索引的葉節點包含了實際的數據行。
![[Pasted image 20250116161322.png]]

InnoDB 在創建聚簇索引時，會根據不同的場景選擇不同的列作為索引：

- 如果有主鍵，默認會使用主鍵作為聚簇索引的索引鍵；
- 如果沒有主鍵，就選擇第一個不包含 NULL 值的唯一列作為聚簇索引的索引鍵；
- 在上面兩個都沒有的情況下，InnoDB 將自動生成一個隐式自增 id 列作為聚簇索引的索引鍵；

一張表隻能有一個聚簇索引，那為了實現非主鍵字段的快速搜索，就引出了二級索引（非聚簇索引 / 輔助索引），它也是利用了 B+ 樹的數據結構，但是二級索引的葉子節點存放的是主鍵值，不是實際數據。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BB%80%E4%B9%88%E5%AD%97%E6%AE%B5%E9%80%82%E5%90%88%E5%BD%93%E5%81%9A%E4%B8%BB%E9%94%AE)什麽字段适合當做主鍵？

- 字段具有唯一性，且不能為空的特性
- 字段最好的是有遞增的趨勢的，如果字段的值是随機無序的，可能會引發頁分裂的問題，造型性能影響。
- 不建議用業務數據作為主鍵，比如會員卡號、訂單號、學生號之類的，因為我們無法預測未來會不會因為業務需要，而出現業務字段重複或者重用的情況。
- 通常情況下會用自增字段來做主鍵，對於單機系統來說是沒問題的。但是，如果有多台服務器，各自都可以錄入數據，那就不一定适用了。因為如果每台機器各自産生的數據需要合并，就可能會出現主鍵重複的問題，這時候就需要考慮分布式 id 的方案了。

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%80%A7%E5%88%AB%E5%AD%97%E6%AE%B5%E8%83%BD%E5%8A%A0%E7%B4%A2%E5%BC%95%E4%B9%88-%E4%B8%BA%E5%95%A5)性别字段能加索引麽？為啥？

不建議針對性别字段加索引。

實際上與索引創建規則之一區分度有關，性别字段假設有 100w 數據，50w 男、50w 女，區别度幾乎等於 0 。

區分度的計算方式 ：select count (DISTINCT sex)/count (*) from sys_user

實際上對於性别字段不适合創建索引，是因為 select * 操作，還得進行 50w 次回表操作，根據主鍵從聚簇索引中找到其他字段 ，這一部分開銷從上面的測試來說還是比較大的，所以從性能角度來看不建議性别字段加索引，加上索引并不是索引失效，而是回表操作使得變慢的。

既然走索引的查詢的成本比全表掃描高，優化器就會選擇全表掃描的方向進行查詢，這時候建立的性别字段索引就沒有啓到加快查詢的作用，反而還因為創建了索引占用了空間。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%A1%A8%E4%B8%AD%E5%8D%81%E4%B8%AA%E5%AD%97%E6%AE%B5-%E4%BD%A0%E4%B8%BB%E9%94%AE%E7%94%A8%E8%87%AA%E5%A2%9Eid%E8%BF%98%E6%98%AFuuid-%E4%B8%BA%E4%BB%80%E4%B9%88)表中十個字段，你主鍵用自增 ID 還是 UUID，為什麽？

用的是自增 id。

因為 uuid 相對順序的自增 id 來說是毫無規律可言的，新行的值不一定要比之前的主鍵的值要大，所以 innodb 無法做到總是把新行插入到索引的最後，而是需要為新行尋找新的合适的位置從而來分配新的空間。

這個過程需要做很多額外的操作，數據的毫無順序會導致數據分布散亂，將會導致以下的問題：

- 寫入的目標頁很可能已經刷新到磁盤上并且從緩存上移除，或者還沒有被加載到緩存中，innodb 在插入之前不得不先找到并從磁盤讀取目標頁到内存中，這將導致大量的随機 IO。
- 因為寫入是亂序的，innodb 不得不頻繁的做頁分裂操作，以便為新的行分配空間，頁分裂導致移動大量的數據，影響性能。
- 由於頻繁的頁分裂，頁會變得稀疏并被不規則的填充，最終會導致數據會有碎片。

結論：使用 InnoDB 應該盡可能的按主鍵的自增順序插入，并且盡可能使用單調的增加的聚簇鍵的值來插入新行。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BB%80%E4%B9%88%E8%87%AA%E5%A2%9Eid%E6%9B%B4%E5%BF%AB%E4%B8%80%E4%BA%9B-uuid%E4%B8%8D%E5%BF%AB%E5%90%97-%E5%AE%83%E5%9C%A8b-%E6%A0%91%E9%87%8C%E9%9D%A2%E5%AD%98%E5%82%A8%E6%98%AF%E6%9C%89%E5%BA%8F%E7%9A%84%E5%90%97)什麽自增 ID 更快一些，UUID 不快嗎，它在 B + 樹裏面存儲是有序的嗎？

自增的主鍵的值是順序的，所以 Innodb 把每一條記錄都存儲在一條記錄的後面，所以自增 id 更快的原因：

- 下一條記錄就會寫入新的頁中，一旦數據按照這種順序的方式加載，主鍵頁就會近乎於順序的記錄填滿，提升了頁面的最大填充率，不會有頁的浪費
- 新插入的行一定會在原有的最大數據行下一行，mysql 定位和尋址很快，不會為計算新行的位置而做出額外的消耗
- 減少了頁分裂和碎片的産生

但是 UUID 不是遞增的，MySQL 中索引的數據結構是 B+Tree，這種數據結構的特點是索引樹上的節點的數據是有序的，而如果使用 UUID 作為主鍵，那麽每次插入數據時，因為無法保證每次産生的 UUID 有序，所以就會出現新的 UUID 需要插入到索引樹的中間去，這樣可能會頻繁地導致頁分裂，使性能下降。

而且，UUID 態占用内存。每個 UUID 由 36 個字符組成，在字符串進行比較時，需要從前往後比較，字符串越長，性能越差。另外字符串越長，占用的内存越大，由於頁的大小是固定的，這樣一個頁上能存放的關鍵字數量就會越少，這樣最終就會導致索引樹的高度越大，在索引搜索的時候，發生的磁盤 IO 次數越多，性能越差。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E4%B8%AD%E7%9A%84%E7%B4%A2%E5%BC%95%E6%98%AF%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0%E7%9A%84)Mysql 中的索引是怎麽實現的 ？

MySQL InnoDB 引擎是用了 B + 樹作為了索引的數據結構。

B+Tree 是一種多叉樹，葉子節點才存放數據，非葉子節點隻存放索引，而且每個節點裏的數據是**按主鍵順序存放**的。每一層父節點的索引值都會出現在下層子節點的索引值中，因此在葉子節點中，包括了所有的索引值信息，并且每一個葉子節點都有兩個指針，分别指向下一個葉子節點和上一個葉子節點，形成一個雙向鏈表。

主鍵索引的 B+Tree 如圖所示：

![[100_attachements/7b1007c85b5c12eea71989eff92c8f2e_MD5.png]]

比如，我們執行了下面這條查詢語句：

```
select * from product where id= 5;
```

這條語句使用了主鍵索引查詢 id 號為 5 的商品。查詢過程是這樣的，B+Tree 會自頂向下逐層進行查找：

- 將 5 與根節點的索引數據 (1，10，20) 比較，5 在 1 和 10 之間，所以根據 B+Tree 的搜索邏輯，找到第二層的索引數據 (1，4，7)；
- 在第二層的索引數據 (1，4，7) 中進行查找，因為 5 在 4 和 7 之間，所以找到第三層的索引數據（4，5，6）；
- 在葉子節點的索引數據（4，5，6）中進行查找，然後我們找到了索引值為 5 的行數據。

數據庫的索引和數據都是存儲在硬盤的，我們可以把讀取一個節點當作一次磁盤 I/O 操作。那麽上面的整個查詢過程一共經曆了 3 個節點，也就是進行了 3 次 I/O 操作。

B+Tree 存儲千萬級的數據隻需要 3-4 層高度就可以滿足，這意味着從千萬級的表查詢目標數據最多需要 3-4 次磁盤 I/O，所以 **B+Tree 相比於 B 樹和二叉樹來說，最大的優勢在於查詢效率很高，因為即使在數據量很大的情況，查詢一個數據的磁盤 I/O 依然維持在 3-4 次。**

## [#](https://xiaolincoding.com/interview/mysql.html#%E6%9F%A5%E8%AF%A2%E6%95%B0%E6%8D%AE%E6%97%B6-%E5%88%B0%E4%BA%86b-%E6%A0%91%E7%9A%84%E5%8F%B6%E5%AD%90%E8%8A%82%E7%82%B9-%E4%B9%8B%E5%90%8E%E7%9A%84%E6%9F%A5%E6%89%BE%E6%95%B0%E6%8D%AE%E6%98%AF%E5%A6%82%E4%BD%95%E5%81%9A)查詢數據時，到了 B + 樹的葉子節點，之後的查找數據是如何做？

**數據頁中的記錄按照「主鍵」順序組成單向鏈表**，單向鏈表的特點就是插入、删除非常方便，但是檢索效率不高，最差的情況下需要遍曆鏈表上的所有節點才能完成檢索。

因此，數據頁中有一個**頁目錄**，起到記錄的索引作用，就像我們書那樣，針對書中内容的每個章節設立了一個目錄，想看某個章節的時候，可以查看目錄，快速找到對應的章節的頁數，而數據頁中的頁目錄就是為了能快速找到記錄。那 InnoDB 是如何給記錄創建頁目錄的呢？


1. 將所有的記錄劃分成幾個組，這些記錄包括最小記錄和最大記錄，但不包括標記為 “已删除” 的記錄；
2. 每個記錄組的最後一條記錄就是組内最大的那條記錄，并且最後一條記錄的頭信息中會存儲該組一共有多少條記錄，作為 n_owned 字段（上圖中粉紅色字段）
3. 頁目錄用來存儲每組最後一條記錄的地址偏移量，這些地址偏移量會按照先後順序存儲起來，每組的地址偏移量也被稱之為槽（slot），每個槽相當於指針指向了不同組的最後一個記錄。

從圖可以看到，**頁目錄就是由多個槽組成的，槽相當於分組記錄的索引**。然後，因為記錄是按照「主鍵值」從小到大排序的，所以**我們通過槽查找記錄時，可以使用二分法快速定位要查詢的記錄在哪個槽（哪個記錄分組），定位到槽後，再遍曆槽内的所有記錄，找到對應的記錄**，無需從最小記錄開始遍曆整個頁中的記錄鏈表。以上面那張圖舉個例子，5 個槽的編號分别為 0，1，2，3，4，我想查找主鍵為 11 的用戶記錄：

- 先二分得出槽中間位是 (0+4)/2=2 ，2 號槽裏最大的記錄為 8。因為 11 > 8，所以需要從 2 號槽後繼續搜索記錄；
- 再使用二分搜索出 2 號和 4 槽的中間位是 (2+4)/2= 3，3 號槽裏最大的記錄為 12。因為 11 < 12，所以主鍵為 11 的記錄在 3 號槽裏；
- 再從 3 號槽指向的主鍵值為 9 記錄開始向下搜索 2 次，定位到主鍵為 11 的記錄，取出該條記錄的信息即為我們想要查找的内容。

### [#](https://xiaolincoding.com/interview/mysql.html#)

### [#](https://xiaolincoding.com/interview/mysql.html#b-%E6%A0%91%E7%9A%84%E7%89%B9%E6%80%A7%E6%98%AF%E4%BB%80%E4%B9%88)B + 樹的特性是什麽？

- **所有葉子節點都在同一層**：這是 B + 樹的一個重要特性，确保了所有數據項的檢索都具有相同的 I/O 延遲，提高了搜索效率。每個葉子節點都包含指向相鄰葉子節點的指針，形成一個鏈表，由於葉子節點之間的鏈接，B + 樹非常适合進行範圍查詢和排序掃描。可以沿着葉子節點的鏈表順序訪問數據，而無需進行多次随機訪問。
- **非葉子節點存儲鍵值**：非葉子節點僅存儲鍵值和指向子節點的指針，不包含數據記錄。這些鍵值用於指導搜索路徑，幫助快速定位到正确的葉子節點。并且，由於非葉子節點隻存放鍵值，當數據量比較大時，相對於 B 樹，B + 樹的層高更少，查找效率也就更高。
- **葉子節點存儲數據記錄**：與 B 樹不同，B + 樹的葉子節點存儲實際的數據記錄或指向數據記錄的指針。這意味着每次搜索都會到達葉子節點，才能找到所需數據。
- **自平衡**：B + 樹在插入、删除和更新操作後會自動重新平衡，确保樹的高度保持相對穩定，從而保持良好的搜索性能。每個節點最多可以有 M 個子節點，最少可以有 ceil (M/2) 個子節點（除了根節點），這裏的 M 是樹的階數。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%AF%B4%E8%AF%B4b-%E6%A0%91%E5%92%8Cb%E6%A0%91%E7%9A%84%E5%8C%BA%E5%88%AB)說說 B + 樹和 B 樹的區别

- 在 B + 樹中，數據都存儲在葉子節點上，而非葉子節點隻存儲索引信息；而 B 樹的非葉子節點既存儲索引信息也存儲部分數據。
- B + 樹的葉子節點使用鏈表相連，便於範圍查詢和順序訪問；B 樹的葉子節點沒有鏈表連接。
- B + 樹的查找性能更穩定，每次查找都需要查找到葉子節點；而 B 樹的查找可能會在非葉子節點找到數據，性能相對不穩定。

### [#](https://xiaolincoding.com/interview/mysql.html#b-%E6%A0%91%E7%9A%84%E5%A5%BD%E5%A4%84%E6%98%AF%E4%BB%80%E4%B9%88)B + 樹的好處是什麽？

B 樹和 B+ 都是通過多叉樹的方式，會將樹的高度變矮，所以這兩個數據結構非常适合檢索存於磁盤中的數據。

但是 MySQL 默認的存儲引擎 InnoDB 采用的是 B+ 作為索引的數據結構，原因有：
![[Pasted image 20250116161347.png]]

- B+ 樹的非葉子節點不存放實際的記錄數據，僅存放索引，因此數據量相同的情況下，相比存儲即存索引又存記錄的 B 樹，B + 樹的非葉子節點可以存放更多的索引，因此 B+ 樹可以比 B 樹更「矮胖」，查詢底層節點的磁盤 I/O 次數會更少。
- B+ 樹有大量的冗餘節點（所有非葉子節點都是冗餘索引），這些冗餘索引讓 B+ 樹在插入、删除的效率都更高，比如删除根節點的時候，不會像 B 樹那樣會發生複雜的樹的變化；
- B+ 樹葉子節點之間用鏈表連接了起來，有利於範圍查詢，而 B 樹要實現範圍查詢，因此隻能通過樹的遍曆來完成範圍查詢，這會涉及多個節點的磁盤 I/O 操作，範圍查詢效率不如 B+ 樹。

### [#](https://xiaolincoding.com/interview/mysql.html#b-%E6%A0%91%E7%9A%84%E5%8F%B6%E5%AD%90%E8%8A%82%E7%82%B9%E9%93%BE%E8%A1%A8%E6%98%AF%E5%8D%95%E5%90%91%E8%BF%98%E6%98%AF%E5%8F%8C%E5%90%91)B + 樹的葉子節點鏈表是單向還是雙向？

雙向的，為了實現倒序遍曆或者排序。
![[Pasted image 20250116161357.png]]

Innodb 使用的 B+ 樹有一些特别的點，比如：

- B+ 樹的葉子節點之間是用「雙向鏈表」進行連接，這樣的好處是既能向右遍曆，也能向左遍曆。
- B+ 樹點節點内容是數據頁，數據頁裏存放了用戶的記錄以及各種信息，每個數據頁默認大小是 16 KB。

Innodb 根據索引類型不同，分為聚集和二級索引。他們區别在於，聚集索引的葉子節點存放的是實際數據，所有完整的用戶記錄都存放在聚集索引的葉子節點，而二級索引的葉子節點存放的是主鍵值，而不是實際數據。

因為表的數據都是存放在聚集索引的葉子節點裏，所以 InnoDB 存儲引擎一定會為表創建一個聚集索引，且由於數據在物理上隻會保存一份，所以聚簇索引隻能有一個，而二級索引可以創建多個。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E4%B8%BA%E4%BB%80%E4%B9%88%E7%94%A8b-%E6%A0%91%E7%BB%93%E6%9E%84-%E5%92%8C%E5%85%B6%E4%BB%96%E7%BB%93%E6%9E%84%E6%AF%94%E7%9A%84%E4%BC%98%E7%82%B9)MySQL 為什麽用 B + 樹結構？和其他結構比的優點？

- **B+Tree vs B Tree：**B+Tree 隻在葉子節點存儲數據，而 B 樹 的非葉子節點也要存儲數據，所以 B+Tree 的單個節點的數據量更小，在相同的磁盤 I/O 次數下，就能查詢更多的節點。另外，B+Tree 葉子節點采用的是雙鏈表連接，适合 MySQL 中常見的基於範圍的順序查找，而 B 樹無法做到這一點。
- **B+Tree vs 二叉樹：** 對於有 N 個葉子節點的 B+Tree，其搜索複雜度為 O (logdN)，其中 d 表示節點允許的最大子節點個數為 d 個。在實際的應用當中， d 值是大於 100 的，這樣就保證了，即使數據達到千萬級别時，B+Tree 的高度依然維持在 3~4 層左右，也就是說一次數據查詢操作隻需要做 3~4 次的磁盤 I/O 操作就能查詢到目標數據。而二叉樹的每個父節點的兒子節點個數隻能是 2 個，意味着其搜索複雜度為 O (logN)，這已經比 B+Tree 高出不少，因此二叉樹檢索到目標數據所經曆的磁盤 I/O 次數要更多。
- **B+Tree vs Hash：**Hash 在做等值查詢的時候效率賊快，搜索複雜度為 O (1)。但是 Hash 表不适合做範圍查詢，它更适合做等值的查詢，這也是 B+Tree 索引要比 Hash 表索引有着更廣泛的适用場景的原因

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%B8%BA%E4%BB%80%E4%B9%88-myssql-%E4%B8%8D%E7%94%A8-%E8%B7%B3%E8%A1%A8)為什麽 MysSQL 不用 跳表？

B + 樹的高度在 3 層時存儲的數據可能已達千萬級别，但對於跳表而言同樣去維護千萬的數據量那麽所造成的跳表層數過高而導致的磁盤 io 次數增多，也就是使用 B + 樹在存儲同樣的數據下磁盤 io 次數更少。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95%E7%9A%84%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86)聯合索引的實現原理？

將將多個字段組合成一個索引，該索引就被稱為聯合索引。

比如，將商品表中的 product_no 和 name 字段組合成聯合索引 (product_no, name)，創建聯合索引的方式如下：

```
CREATE INDEX index_product_no_name ON product(product_no, name);
```

聯合索引 (product_no, name) 的 B+Tree 示意圖如下：
![[Pasted image 20250116161411.png]]

可以看到，聯合索引的非葉子節點用兩個字段的值作為 B+Tree 的 key 值。當在聯合索引查詢數據時，先按 product_no 字段比較，在 product_no 相同的情況下再按 name 字段比較。

也就是說，聯合索引查詢的 B+Tree 是先按 product_no 進行排序，然後再 product_no 相同的情況再按 name 字段排序。

因此，使用聯合索引時，存在**最左匹配原則**，也就是按照最左優先的方式進行索引的匹配。在使用聯合索引進行查詢的時候，如果不遵循「最左匹配原則」，聯合索引會失效，這樣就無法利用到索引快速查詢的特性了。

比如，如果創建了一個 (a, b, c) 聯合索引，如果查詢條件是以下這幾種，就可以匹配上聯合索引：

- where a=1；
- where a=1 and b=2 and c=3；
- where a=1 and b=2；

需要注意的是，因為有查詢優化器，所以 a 字段在 where 子句的順序并不重要。

但是，如果查詢條件是以下這幾種，因為不符合最左匹配原則，所以就無法匹配上聯合索引，聯合索引就會失效:

- where b=2；
- where c=3；
- where b=2 and c=3；

上面這些查詢條件之所以會失效，是因為 (a, b, c) 聯合索引，是先按 a 排序，在 a 相同的情況再按 b 排序，在 b 相同的情況再按 c 排序。所以，**b 和 c 是全局無序，局部相對有序的**，這樣在沒有遵循最左匹配原則的情況下，是無法利用到索引的。

我這裏舉聯合索引（a，b）的例子，該聯合索引的 B+ Tree 如下：
![[Pasted image 20250116161419.png]]


可以看到，a 是全局有序的（1, 2, 2, 3, 4, 5, 6, 7 ,8），而 b 是全局是無序的（12，7，8，2，3，8，10，5，2）。因此，直接執行 where b = 2 這種查詢條件沒有辦法利用聯合索引的，**利用索引的前提是索引裏的 key 是有序的**。

隻有在 a 相同的情況才，b 才是有序的，比如 a 等於 2 的時候，b 的值為（7，8），這時就是有序的，這個有序狀态是局部的，因此，執行 where a = 2 and b = 7 是 a 和 b 字段能用到聯合索引的，也就是聯合索引生效了。

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%88%9B%E5%BB%BA%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95%E6%97%B6%E9%9C%80%E8%A6%81%E6%B3%A8%E6%84%8F%E4%BB%80%E4%B9%88)創建聯合索引時需要注意什麽？

建立聯合索引時的字段順序，對索引效率也有很大影響。越靠前的字段被用於索引過濾的概率越高，實際開發工作中**建立聯合索引時，要把區分度大的字段排在前面，這樣區分度大的字段越有可能被更多的 SQL 使用到**。

區分度就是某個字段 column 不同值的個數「除以」表的總行數，計算公式如下：
![[Pasted image 20250116161428.png]]

比如，性别的區分度就很小，不适合建立索引或不适合排在聯合索引列的靠前的位置，而 UUID 這類字段就比較适合做索引或排在聯合索引列的靠前的位置。

因為如果索引的區分度很小，假設字段的值分布均勻，那麽無論搜索哪個值都可能得到一半的數據。在這些情況下，還不如不要索引，因為 MySQL 還有一個查詢優化器，查詢優化器發現某個值出現在表的數據行中的百分比（慣用的百分比界線是 "30%"）很高的時候，它一般會忽略索引，進行全表掃描。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95abc-%E7%8E%B0%E5%9C%A8%E6%9C%89%E4%B8%AA%E6%89%A7%E8%A1%8C%E8%AF%AD%E5%8F%A5%E6%98%AFa-xxx-and-c-xxx-%E7%B4%A2%E5%BC%95%E6%80%8E%E4%B9%88%E8%B5%B0)聯合索引 ABC，現在有個執行語句是 A = XXX and C < XXX，索引怎麽走

根據最左匹配原則，A 可以走聯合索引，C 不會走聯合索引，但是 C 可以走索引下推

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95-a-b-c-%E6%9F%A5%E8%AF%A2%E6%9D%A1%E4%BB%B6-where-b-xxx-and-a-x-%E4%BC%9A%E7%94%9F%E6%95%88%E5%90%97)聯合索引 (a,b,c) ，查詢條件 where b > xxx and a = x 會生效嗎

索引會生效，a 和 b 字段都能利用聯合索引，符合聯合索引最左匹配原則。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95-a-b-c-where%E6%9D%A1%E4%BB%B6%E6%98%AF-a-2-and-c-1-%E8%83%BD%E7%94%A8%E5%88%B0%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95%E5%90%97)聯合索引 (a, b，c)，where 條件是 a=2 and c = 1，能用到聯合索引嗎？

會用到聯合索引，但是隻有 a 才能走索引，c 無法走索引，因為不符合最左匹配原則。雖然 c 無法走索引， 但是 c 字段在 5.6 版本之後，會有索引下推的優化，能減少回表查詢的次數。

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%B4%A2%E5%BC%95%E5%A4%B1%E6%95%88%E6%9C%89%E5%93%AA%E4%BA%9B)索引失效有哪些？

6 種會發生索引失效的情況：

- 當我們使用左或者左右模糊匹配的時候，也就是 like % xx 或者 like % xx% 這兩種方式都會造成索引失效；
- 當我們在查詢條件中對索引列使用函數，就會導致索引失效。
- 當我們在查詢條件中對索引列進行表達式計算，也是無法走索引的。
- MySQL 在遇到字符串和數字比較的時候，會自動把字符串轉為數字，然後再進行比較。如果字符串是索引列，而條件語句中的輸入參數是數字的話，那麽索引列會發生隐式類型轉換，由於隐式類型轉換是通過 CAST 函數實現的，等同於對索引列使用了函數，所以就會導致索引失效。
- 聯合索引要能正确使用需要遵循最左匹配原則，也就是按照最左優先的方式進行索引的匹配，否則就會導致索引失效。
- 在 WHERE 子句中，如果在 OR 前的條件列是索引列，而在 OR 後的條件列不是索引列，那麽索引會失效。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BB%80%E4%B9%88%E6%83%85%E5%86%B5%E4%B8%8B%E4%BC%9A%E5%9B%9E%E8%A1%A8%E6%9F%A5%E8%AF%A2)什麽情況下會回表查詢

從物理存儲的角度來看，索引分為聚簇索引（主鍵索引）、二級索引（輔助索引）。

它們的主要區别如下：

- 主鍵索引的 B+Tree 的葉子節點存放的是實際數據，所有完整的用戶記錄都存放在主鍵索引的 B+Tree 的葉子節點裏；
- 二級索引的 B+Tree 的葉子節點存放的是主鍵值，而不是實際數據。

所以，在查詢時使用了二級索引，如果查詢的數據能在二級索引裏查詢的到，那麽就不需要回表，這個過程就是覆蓋索引。

**如果查詢的數據不在二級索引裏，就會先檢索二級索引，找到對應的葉子節點，獲取到主鍵值後，然後再檢索主鍵索引，就能查詢到數據了，這個過程就是回表**。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BB%80%E4%B9%88%E6%98%AF%E8%A6%86%E7%9B%96%E7%B4%A2%E5%BC%95)什麽是覆蓋索引？

覆蓋索引是指一個索引包含了查詢所需的所有列，因此不需要訪問表中的數據行就能完成查詢。

換句話說，查詢所需的所有數據都能從索引中直接獲取，而不需要進行回表查詢。覆蓋索引能夠顯著提高查詢性能，因為減少了訪問數據頁的次數，從而減少了 I/O 操作。

假設有一張表 employees，表結構如下：

```
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(100),
  age INT,
  department VARCHAR(100),
  salary DECIMAL(10, 2)
);

CREATE INDEX idx_name_age_department ON employees(name, age, department);
```

如果我們有以下查詢：

```
SELECT name, age, department FROM employees WHERE name = 'John';
```

在這種情況下，idx_name_age_department 是一個覆蓋索引，因為它包含了查詢所需的所有列：name、age 和 department。查詢可以完全在索引層完成，而不需要訪問表中的數據行。

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%A6%82%E6%9E%9C%E4%B8%80%E4%B8%AA%E5%88%97%E5%8D%B3%E4%BD%BF%E5%8D%95%E5%88%97%E7%B4%A2%E5%BC%95-%E5%8F%88%E6%98%AF%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95-%E5%8D%95%E7%8B%AC%E6%9F%A5%E5%AE%83%E7%9A%84%E8%AF%9D%E5%85%88%E8%B5%B0%E5%93%AA%E4%B8%AA)如果一個列即使單列索引，又是聯合索引，單獨查它的話先走哪個？

mysql 優化器會分析每個索引的查詢成本，然後選擇成本最低的方案來執行 sql。

如果單列索引是 a，聯合索引是（a ，b），那麽針對下面這個查詢：

```
select a, b from table where a = ? and b =?
```

優化器會選擇聯合索引，因為查詢成本更低，查詢也不需要回表，直接索引覆蓋了。

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%B4%A2%E5%BC%95%E5%B7%B2%E7%BB%8F%E5%BB%BA%E5%A5%BD%E4%BA%86-%E9%82%A3%E6%88%91%E5%86%8D%E6%8F%92%E5%85%A5%E4%B8%80%E6%9D%A1%E6%95%B0%E6%8D%AE-%E7%B4%A2%E5%BC%95%E4%BC%9A%E6%9C%89%E5%93%AA%E4%BA%9B%E5%8F%98%E5%8C%96)索引已經建好了，那我再插入一條數據，索引會有哪些變化？

插入新數據可能導致 B + 樹結構的調整和索引信息的更新，以保持 B + 樹的平衡性和正确性，這些變化通常由數據庫系統自動處理，确保數據的一致性和索引的有效性。

如果插入的數據導致葉子節點已滿，可能會觸發葉子節點的分裂操作，以保持 B + 樹的平衡性。

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%B4%A2%E5%BC%95%E5%AD%97%E6%AE%B5%E6%98%AF%E4%B8%8D%E6%98%AF%E5%BB%BA%E7%9A%84%E8%B6%8A%E5%A4%9A%E8%B6%8A%E5%A5%BD)索引字段是不是建的越多越好？

不是，建的的越多會占用越多的空間，而且在寫入頻繁的場景下，對於 B + 樹的維護所付出的性能消耗也會越大

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%A6%82%E6%9E%9C%E6%9C%89%E4%B8%80%E4%B8%AA%E5%AD%97%E6%AE%B5%E6%98%AFstatus%E5%80%BC%E4%B8%BA0%E6%88%96%E8%80%851-%E9%80%82%E5%90%88%E5%BB%BA%E7%B4%A2%E5%BC%95%E5%90%97)如果有一個字段是 status 值為 0 或者 1，适合建索引嗎

不适合，區分度低的字段不适合建立索引。

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%B4%A2%E5%BC%95%E7%9A%84%E4%BC%98%E7%BC%BA%E7%82%B9)索引的優缺點？

索引最大的好處是提高查詢速度，但是索引也是有缺點的，比如：

- 需要占用物理空間，數量越大，占用空間越大；
- 創建索引和維護索引要耗費時間，這種時間随着數據量的增加而增大；
- 會降低表的增删改的效率，因為每次增删改索引，B+ 樹為了維護索引有序性，都需要進行動态維護。

所以，索引不是萬能鑰匙，它也是根據場景來使用的。

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%80%8E%E4%B9%88%E5%86%B3%E5%AE%9A%E5%BB%BA%E7%AB%8B%E5%93%AA%E4%BA%9B%E7%B4%A2%E5%BC%95)怎麽決定建立哪些索引？

> 什麽時候适用索引？

- 字段有唯一性限制的，比如商品編碼；
- 經常用於 `WHERE` 查詢條件的字段，這樣能夠提高整個表的查詢速度，如果查詢條件不是一個字段，可以建立聯合索引。
- 經常用於 `GROUP BY` 和 `ORDER BY` 的字段，這樣在查詢的時候就不需要再去做一次排序了，因為我們都已經知道了建立索引之後在 B+Tree 中的記錄都是排序好的。

> 什麽時候不需要創建索引？

- `WHERE` 條件，`GROUP BY`，`ORDER BY` 裏用不到的字段，索引的價值是快速定位，如果起不到定位的字段通常是不需要創建索引的，因為索引是會占用物理空間的。
- 字段中存在大量重複數據，不需要創建索引，比如性别字段，隻有男女，如果數據庫表中，男女的記錄分布均勻，那麽無論搜索哪個值都可能得到一半的數據。在這些情況下，還不如不要索引，因為 MySQL 還有一個查詢優化器，查詢優化器發現某個值出現在表的數據行中的百分比很高的時候，它一般會忽略索引，進行全表掃描。
- 表數據態少的時候，不需要創建索引；
- 經常更新的字段不用創建索引，比如不要對電商項目的用戶餘額建立索引，因為索引字段頻繁修改，由

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%B4%A2%E5%BC%95%E4%BC%98%E5%8C%96%E8%AF%A6%E7%BB%86%E8%AE%B2%E8%AE%B2)索引優化詳細講講

常見優化索引的方法：

- 前綴索引優化：使用前綴索引是為了減小索引字段大小，可以增加一個索引頁中存儲的索引值，有效提高索引的查詢速度。在一些大字符串的字段作為索引時，使用前綴索引可以幫助我們減小索引項的大小。
- 覆蓋索引優化：覆蓋索引是指 SQL 中 query 的所有字段，在索引 B+Tree 的葉子節點上都能找得到的那些索引，從二級索引中查詢得到記錄，而不需要通過聚簇索引查詢獲得，可以避免回表的操作。
- 主鍵索引最好是自增的：
    - 如果我們使用自增主鍵，那麽每次插入的新數據就會按順序添加到當前索引節點的位置，不需要移動已有的數據，當頁面寫滿，就會自動開辟一個新頁面。因為每次**插入一條新記錄，都是追加操作，不需要重新移動數據**，因此這種插入數據的方法效率非常高。
    - 如果我們使用非自增主鍵，由於每次插入主鍵的索引值都是随機的，因此每次插入新的數據時，就可能會插入到現有數據頁中間的某個位置，這將不得不移動其它數據來滿足新數據的插入，甚至需要從一個頁面複制數據到另外一個頁面，我們通常將這種情況稱為**頁分裂**。頁分裂還有可能會造成大量的内存碎片，導致索引結構不緊湊，從而影響查詢效率。
- 防止索引失效：
    - 當我們使用左或者左右模糊匹配的時候，也就是 `like %xx` 或者 `like %xx%` 這兩種方式都會造成索引失效；
    - 當我們在查詢條件中對索引列做了計算、函數、類型轉換操作，這些情況下都會造成索引失效；
    - 聯合索引要能正确使用需要遵循最左匹配原則，也就是按照最左優先的方式進行索引的匹配，否則就會導致索引失效。
    - 在 WHERE 子句中，如果在 OR 前的條件列是索引列，而在 OR 後的條件列不是索引列，那麽索引會失效。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BA%86%E8%A7%A3%E8%BF%87%E5%89%8D%E7%BC%80%E7%B4%A2%E5%BC%95%E5%90%97)了解過前綴索引嗎？

使用前綴索引是為了減小索引字段大小，可以增加一個索引頁中存儲的索引值，有效提高索引的查詢速度。在一些大字符串的字段作為索引時，使用前綴索引可以幫助我們減小索引項的大小。

## [#](https://xiaolincoding.com/interview/mysql.html#%E4%BA%8B%E5%8A%A1)事務

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BA%8B%E5%8A%A1%E7%9A%84%E7%89%B9%E6%80%A7%E6%98%AF%E4%BB%80%E4%B9%88-%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E7%9A%84)事務的特性是什麽？如何實現的？

- **原子性（Atomicity）**：一個事務中的所有操作，要麽全部完成，要麽全部不完成，不會結束在中間某個環節，而且事務在執行過程中發生錯誤，會被回滾到事務開始前的狀态，就像這個事務從來沒有執行過一樣，就好比買一件商品，購買成功時，則給商家付了錢，商品到手；購買失敗時，則商品在商家手中，消費者的錢也沒花出去。
- **一致性（Consistency）**：是指事務操作前和操作後，數據滿足完整性約束，數據庫保持一致性狀态。比如，用戶 A 和用戶 B 在銀行分别有 800 元和 600 元，總共 1400 元，用戶 A 給用戶 B 轉賬 200 元，分為兩個步驟，從 A 的賬戶扣除 200 元和對 B 的賬戶增加 200 元。一致性就是要求上述步驟操作後，最後的結果是用戶 A 還有 600 元，用戶 B 有 800 元，總共 1400 元，而不會出現用戶 A 扣除了 200 元，但用戶 B 未增加的情況（該情況，用戶 A 和 B 均為 600 元，總共 1200 元）。
- **隔離性（Isolation）**：數據庫允許多個并發事務同時對其數據進行讀寫和修改的能力，隔離性可以防止多個事務并發執行時由於交叉執行而導致數據的不一致，因為多個事務同時使用相同的數據時，不會相互幹擾，每個事務都有一個完整的數據空間，對其他并發事務是隔離的。也就是說，消費者購買商品這個事務，是不影響其他消費者購買的。
- **持久性（Durability）**：事務處理結束後，對數據的修改就是永久的，即便系統故障也不會丢失。

MySQL InnoDB 引擎通過什麽技術來保證事務的這四個特性的呢？

- 持久性是通過 redo log （重做日志）來保證的；
- 原子性是通過 undo log（回滾日志） 來保證的；
- 隔離性是通過 MVCC（多版本并發控制） 或鎖機制來保證的；
- 一致性則是通過持久性 + 原子性 + 隔離性來保證；

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E5%8F%AF%E8%83%BD%E5%87%BA%E7%8E%B0%E4%BB%80%E4%B9%88%E5%92%8C%E5%B9%B6%E5%8F%91%E7%9B%B8%E5%85%B3%E9%97%AE%E9%A2%98)mysql 可能出現什麽和并發相關問題？

MySQL 服務端是允許多個客戶端連接的，這意味着 MySQL 會出現同時處理多個事務的情況。

那麽**在同時處理多個事務的時候，就可能出現髒讀（dirty read）、不可重複讀（non-repeatable read）、幻讀（phantom read）的問題**。

接下來，通過舉例子給大家說明，這些問題是如何發生的。

> 髒讀

**如果一個事務「讀到」了另一個「未提交事務修改過的數據」，就意味着發生了「髒讀」現象。**

舉個栗子。

假設有 A 和 B 這兩個事務同時在處理，事務 A 先開始從數據庫中讀取小林的餘額數據，然後再執行更新操作，如果此時事務 A 還沒有提交事務，而此時正好事務 B 也從數據庫中讀取小林的餘額數據，那麽事務 B 讀取到的餘額數據是剛才事務 A 更新後的數據，即使沒有提交事務。
![[Pasted image 20250116161506.png]]


因為事務 A 是還沒提交事務的，也就是它随時可能發生回滾操作，**如果在上面這種情況事務 A 發生了回滾，那麽事務 B 剛才得到的數據就是過期的數據，這種現象就被稱為髒讀。**

> 不可重複讀

**在一個事務内多次讀取同一個數據，如果出現前後兩次讀到的數據不一樣的情況，就意味着發生了「不可重複讀」現象。**

舉個栗子。

假設有 A 和 B 這兩個事務同時在處理，事務 A 先開始從數據庫中讀取小林的餘額數據，然後繼續執行代碼邏輯處理，** 在這過程中如果事務 B 更新了這條數據，并提交了事務，那麽當事務 A 再次讀取該數據時，就會發現前後兩次讀到的數據是不一致的，這種現象就被稱為不可重複讀。

**
![[Pasted image 20250116161522.png]]

> 幻讀

**在一個事務内多次查詢某個符合查詢條件的「記錄數量」，如果出現前後兩次查詢到的記錄數量不一樣的情況，就意味着發生了「幻讀」現象。**

舉個栗子。

假設有 A 和 B 這兩個事務同時在處理，事務 A 先開始從數據庫查詢賬戶餘額大於 100 萬的記錄，發現共有 5 條，然後事務 B 也按相同的搜索條件也是查詢出了 5 條記錄。

![[Pasted image 20250116161528.png]]


接下來，事務 A 插入了一條餘額超過 100 萬的賬號，并提交了事務，此時數據庫超過 100 萬餘額的賬號個數就變為 6。

然後事務 B 再次查詢賬戶餘額大於 100 萬的記錄，此時查詢到的記錄數量有 6 條，**發現和前一次讀到的記錄數量不一樣了，就感覺發生了幻覺一樣，這種現象就被稱為幻讀。**

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%93%AA%E4%BA%9B%E5%9C%BA%E6%99%AF%E4%B8%8D%E9%80%82%E5%90%88%E8%84%8F%E8%AF%BB-%E4%B8%BE%E4%B8%AA%E4%BE%8B%E5%AD%90)哪些場景不适合髒讀，舉個例子？

髒讀是指一個事務在讀取到另一個事務未提交的數據時發生。髒讀可能會導致不一致的數據被讀取，并可能引起問題。以下是一些不适合髒讀的場景：

- **銀行系統**：在銀行系統中，如果一個賬戶的餘額正在被調整但尚未提交，另一個事務讀取了這個臨時的餘額，可能會導致客戶看到不正确的餘額。
- **庫存管理系統**：在一個庫存管理系統中，如果一個商品的數量正在被更新但尚未提交，另一個事務讀取了這個臨時的數量，可能會導致庫存管理錯誤。
- **在線訂單系統**：在一個在線訂單系統中，如果一個訂單正在被修改但尚未提交，另一個事務讀取了這個臨時的訂單狀态，可能導致訂單狀态顯示錯誤，客戶收到不準确的信息。

在以上這些場景中，髒讀可能導致嚴重的問題，因此應該避免發生髒讀，保證數據的一致性和準确性。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E7%9A%84%E6%98%AF%E6%80%8E%E4%B9%88%E8%A7%A3%E5%86%B3%E5%B9%B6%E5%8F%91%E9%97%AE%E9%A2%98%E7%9A%84)mysql 的是怎麽解決并發問題的？

- 鎖機制：Mysql 提供了多種鎖機制來保證數據的一致性，包括行級鎖、表級鎖、頁級鎖等。通過鎖機制，可以在讀寫操作時對數據進行加鎖，确保同時隻有一個操作能夠訪問或修改數據。
- 事務隔離級别：Mysql 提供了多種事務隔離級别，包括讀未提交、讀已提交、可重複讀和串行化。通過設置合适的事務隔離級别，可以在多個事務并發執行時，控制事務之間的隔離程度，以避免數據不一致的問題。
- MVCC（多版本并發控制）：Mysql 使用 MVCC 來管理并發訪問，它通過在數據庫中保存不同版本的數據來實現不同事務之間的隔離。在讀取數據時，Mysql 會根據事務的隔離級别來選擇合适的數據版本，從而保證數據的一致性。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BA%8B%E5%8A%A1%E7%9A%84%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB%E6%9C%89%E5%93%AA%E4%BA%9B)事務的隔離級别有哪些？

- **讀未提交（read uncommitted）**，指一個事務還沒提交時，它做的變更就能被其他事務看到；
- **讀提交（read committed）**，指一個事務提交之後，它做的變更才能被其他事務看到；
- **可重複讀（repeatable read）**，指一個事務執行過程中看到的數據，一直跟這個事務啓動時看到的數據是一致的，**MySQL InnoDB 引擎的默認隔離級别**；
- **串行化（serializable）**；會對記錄加上讀寫鎖，在多個事務對這條記錄進行讀寫操作時，如果發生了讀寫沖突的時候，後訪問的事務必須等前一個事務執行完成，才能繼續執行；

按隔離水平高低排序如下：


針對不同的隔離級别，并發事務時可能發生的現象也會不同。


- 在「讀未提交」隔離級别下，可能發生髒讀、不可重複讀和幻讀現象；
- 在「讀提交」隔離級别下，可能發生不可重複讀和幻讀現象，但是不可能發生髒讀現象；
- 在「可重複讀」隔離級别下，可能發生幻讀現象，但是不可能髒讀和不可重複讀現象；
- 在「串行化」隔離級别下，髒讀、不可重複讀和幻讀現象都不可能會發生。

接下來，舉個具體的例子來說明這四種隔離級别，有一張賬戶餘額表，裏面有一條賬戶餘額為 100 萬的記錄。然後有兩個并發的事務，事務 A 隻負責查詢餘額，事務 B 則會將我的餘額改成 200 萬，下面是按照時間順序執行兩個事務的行為：

![[Pasted image 20250116161554.png]]


在不同隔離級别下，事務 A 執行過程中查詢到的餘額可能會不同：

- 在「讀未提交」隔離級别下，事務 B 修改餘額後，雖然沒有提交事務，但是此時的餘額已經可以被事務 A 看見了，於是事務 A 中餘額 V1 查詢的值是 200 萬，餘額 V2、V3 自然也是 200 萬了；
- 在「讀提交」隔離級别下，事務 B 修改餘額後，因為沒有提交事務，所以事務 A 中餘額 V1 的值還是 100 萬，等事務 B 提交完後，最新的餘額數據才能被事務 A 看見，因此額 V2、V3 都是 200 萬；
- 在「可重複讀」隔離級别下，事務 A 隻能看見啓動事務時的數據，所以餘額 V1、餘額 V2 的值都是 100 萬，當事務 A 提交事務後，就能看見最新的餘額數據了，所以餘額 V3 的值是 200 萬；
- 在「串行化」隔離級别下，事務 B 在執行將餘額 100 萬修改為 200 萬時，由於此前事務 A 執行了讀操作，這樣就發生了讀寫沖突，於是就會被鎖住，直到事務 A 提交後，事務 B 才可以繼續執行，所以從 A 的角度看，餘額 V1、V2 的值是 100 萬，餘額 V3 的值是 200 萬。

這四種隔離級别具體是如何實現的呢？

- 對於「讀未提交」隔離級别的事務來說，因為可以讀到未提交事務修改的數據，所以直接讀取最新的數據就好了；
- 對於「串行化」隔離級别的事務來說，通過加讀寫鎖的方式來避免并行訪問；
- 對於「讀提交」和「可重複讀」隔離級别的事務來說，它們是通過 Read View 來實現的，它們的區别在於創建 Read View 的時機不同，**「讀提交」隔離級别是在「每個語句執行前」都會重新生成一個 Read View，而「可重複讀」隔離級别是「啓動事務時」生成一個 Read View，然後整個事務期間都在用這個 Read View**。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E9%BB%98%E8%AE%A4%E7%BA%A7%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88)mysql 默認級别是什麽？

可重複讀隔離級别

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%8F%AF%E9%87%8D%E5%A4%8D%E8%AF%BB%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB%E4%B8%8B-a%E4%BA%8B%E5%8A%A1%E6%8F%90%E4%BA%A4%E7%9A%84%E6%95%B0%E6%8D%AE-%E5%9C%A8b%E4%BA%8B%E5%8A%A1%E8%83%BD%E7%9C%8B%E8%A7%81%E5%90%97)可重複讀隔離級别下，A 事務提交的數據，在 B 事務能看見嗎？

可重複讀隔離級是由 MVCC（多版本并發控制）實現的，實現的方式是開始事務後（執行 begin 語句後），在執行第一個查詢語句後，會創建一個 Read View，**後續的查詢語句利用這個 Read View，通過這個 Read View 就可以在 undo log 版本鏈找到事務開始時的數據，所以事務過程中每次查詢的數據都是一樣的**，即使中途有其他事務插入了新紀錄，是查詢不出來這條數據的。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%B8%BE%E4%B8%AA%E4%BE%8B%E5%AD%90%E8%AF%B4%E5%8F%AF%E9%87%8D%E5%A4%8D%E8%AF%BB%E4%B8%8B%E7%9A%84%E5%B9%BB%E8%AF%BB%E9%97%AE%E9%A2%98)舉個例子說可重複讀下的幻讀問題

**可重複讀隔離級别下雖然很大程度上避免了幻讀，但是還是沒有能完全解決幻讀**。

我舉例一個可重複讀隔離級别發生幻讀現象的場景。以這張表作為例子：
![[Pasted image 20250116161612.png]]

事務 A 執行查詢 id = 5 的記錄，此時表中是沒有該記錄的，所以查詢不出來。

```
# 事務 A
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from t_stu where id = 5;
Empty set (0.01 sec)
```

然後事務 B 插入一條 id = 5 的記錄，并且提交了事務。

```
# 事務 B
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into t_stu values(5, '小美', 18);
Query OK, 1 row affected (0.00 sec)

mysql> commit;
Query OK, 0 rows affected (0.00 sec)
```

此時，**事務 A 更新 id = 5 這條記錄，對沒錯，事務 A 看不到 id = 5 這條記錄，但是他去更新了這條記錄，這場景确實很違和，然後再次查詢 id = 5 的記錄，事務 A 就能看到事務 B 插入的紀錄了，幻讀就是發生在這種違和的場景**。

```
# 事務 A
mysql> update t_stu set name = '小林coding' where id = 5;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from t_stu where id = 5;
+----+--------------+------+
| id | name         | age  |
+----+--------------+------+
|  5 | 小林coding   |   18 |
+----+--------------+------+
1 row in set (0.00 sec)
```

整個發生幻讀的時序圖如下：

在可重複讀隔離級别下，事務 A 第一次執行普通的 select 語句時生成了一個 ReadView，之後事務 B 向表中新插入了一條 id = 5 的記錄并提交。接着，事務 A 對 id = 5 這條記錄進行了更新操作，在這個時刻，這條新記錄的 trx_id 隐藏列的值就變成了事務 A 的事務 id，之後事務 A 再使用普通 select 語句去查詢這條記錄時就可以看到這條記錄了，於是就發生了幻讀。

因為這種特殊現象的存在，所以我們認為 **MySQL Innodb 中的 MVCC 并不能完全避免幻讀現象**。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql-%E8%AE%BE%E7%BD%AE%E4%BA%86%E5%8F%AF%E9%87%8D%E8%AF%BB%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%90%8E-%E6%80%8E%E4%B9%88%E4%BF%9D%E8%AF%81%E4%B8%8D%E5%8F%91%E7%94%9F%E5%B9%BB%E8%AF%BB)Mysql 設置了可重讀隔離級後，怎麽保證不發生幻讀？

**盡量在開啓事務之後，馬上執行 select ... for update 這類鎖定讀的語句**，因為它會對記錄加 next-key lock，從而避免其他事務插入一條新記錄，就避免了幻讀的問題。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%B8%B2%E8%A1%8C%E5%8C%96%E9%9A%94%E7%A6%BB%E7%BA%A7%E5%88%AB%E6%98%AF%E9%80%9A%E8%BF%87%E4%BB%80%E4%B9%88%E5%AE%9E%E7%8E%B0%E7%9A%84)串行化隔離級别是通過什麽實現的？

是通過行級鎖來實現的，序列化隔離級别下，普通的 select 查詢是會對記錄加 S 型的 next-key 鎖，其他事務就沒沒辦法對這些已經加鎖的記錄進行增删改操作了，從而避免了髒讀、不可重複讀和幻讀現象。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%BB%8B%E7%BB%8Dmvcc%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86)介紹 MVCC 實現原理

MVCC 允許多個事務同時讀取同一行數據，而不會彼此阻塞，每個事務看到的數據版本是該事務開始時的數據版本。這意味着，如果其他事務在此期間修改了數據，正在運行的事務仍然看到的是它開始時的數據狀态，從而實現了非阻塞讀操作。

對於「讀提交」和「可重複讀」隔離級别的事務來說，它們是通過 Read View 來實現的，它們的區别在於創建 Read View 的時機不同，大家可以把 Read View 理解成一個數據快照，就像相機拍照那樣，定格某一時刻的風景。

- 「讀提交」隔離級别是在「每個 select 語句執行前」都會重新生成一個 Read View；
- 「可重複讀」隔離級别是執行第一條 select 時，生成一個 Read View，然後整個事務期間都在用這個 Read View。

Read View 有四個重要的字段：
![[Pasted image 20250116161622.png]]


- m_ids ：指的是在創建 Read View 時，當前數據庫中「活躍事務」的**事務 id 列表**，注意是一個列表， **“活躍事務” 指的就是，啓動了但還沒提交的事務**。
- min_trx_id ：指的是在創建 Read View 時，當前數據庫中「活躍事務」中事務 **id 最小的事務**，也就是 m_ids 的最小值。
- max_trx_id ：這個并不是 m_ids 的最大值，而是**創建 Read View 時當前數據庫中應該給下一個事務的 id 值**，也就是全局事務中最大的事務 id 值 + 1；
- creator_trx_id ：指的是**創建該 Read View 的事務的事務 id**。

對於使用 InnoDB 存儲引擎的數據庫表，它的聚簇索引記錄中都包含下面兩個隐藏列：
![[Pasted image 20250116161631.png]]

- trx_id，當一個事務對某條聚簇索引記錄進行改動時，就會**把該事務的事務 id 記錄在 trx_id 隐藏列裏**；
- roll_pointer，每次對某條聚簇索引記錄進行改動時，都會把舊版本的記錄寫入到 undo 日志中，然後**這個隐藏列是個指針，指向每一個舊版本記錄**，於是就可以通過它找到修改前的記錄。

在創建 Read View 後，我們可以將記錄中的 trx_id 劃分這三種情況：
![[Pasted image 20250116161638.png]]

一個事務去訪問記錄的時候，除了自己的更新記錄總是可見之外，還有這幾種情況：

- 如果記錄的 trx_id 值小於 Read View 中的 min_trx_id 值，表示這個版本的記錄是在創建 Read View **前**已經提交的事務生成的，所以該版本的記錄對當前事務**可見**。
    
- 如果記錄的 trx_id 值大於等於 Read View 中的 max_trx_id 值，表示這個版本的記錄是在創建 Read View **後**才啓動的事務生成的，所以該版本的記錄對當前事務**不可見**。
    
- 如果記錄的 trx_id 值在 Read View 的 min_trx_id 和 max_trx_id 之間，需要判斷 trx_id 是否在 m_ids 列表中：
    
- 如果記錄的 trx_id **在** m_ids 列表中，表示生成該版本記錄的活躍事務依然活躍着（還沒提交事務），所以該版本的記錄對當前事務**不可見**。
    
- 如果記錄的 trx_id **不在** m_ids 列表中，表示生成該版本記錄的活躍事務已經被提交，所以該版本的記錄對當前事務**可見**。
    

**這種通過「版本鏈」來控制并發事務訪問同一個記錄時的行為就叫 MVCC（多版本并發控制）。**

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%B8%80%E6%9D%A1update%E6%98%AF%E4%B8%8D%E6%98%AF%E5%8E%9F%E5%AD%90%E6%80%A7%E7%9A%84-%E4%B8%BA%E4%BB%80%E4%B9%88)一條 update 是不是原子性的？為什麽？

是原子性，主要通過鎖 + undolog 日志保證原子性的

- 執行 update 的時候，會加行級别鎖，保證了一個事務更新一條記錄的時候，不會被其他事務幹擾。
- 事務執行過程中，會生成 undolog，如果事務執行失敗，就可以通過 undolog 日志進行回滾。

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%BB%A5%E7%94%A8%E4%BA%8B%E5%8A%A1-%E6%88%96%E8%80%85%E4%B8%80%E4%B8%AA%E4%BA%8B%E5%8A%A1%E9%87%8C%E6%9C%89%E7%89%B9%E5%88%AB%E5%A4%9Asql%E7%9A%84%E5%BC%8A%E7%AB%AF)濫用事務，或者一個事務裏有特别多 sql 的弊端？

事務的資源在事務提交之後才會釋放的，比如存儲資源、鎖。

如果一個事務特别多 sql，那麽會帶來這些問題：

- 如果一個事務特别多 sql，鎖定的數據態多，容易造成大量的死鎖和鎖超時。
- 回滾記錄會占用大量存儲空間，事務回滾時間長。在 [MySQL (opens new window)](https://cloud.tencent.com/product/cdb?from_column=20065&from=20065)中，實際上每條記錄在更新的時候都會同時記錄一條回滾操作。記錄上的最新值，通過回滾操作，都可以得到前一個狀态的值，sql 越多，所需要保存的回滾數據就越多。
- 執行時間長，容易造成主從延遲，主庫上必須等事務執行完成才會寫入 binlog，再傳給備庫。所以，如果一個主庫上的語句執行 10 分鍾，那這個事務很可能就會導致從庫延遲 10 分鍾

## [#](https://xiaolincoding.com/interview/mysql.html#%E9%94%81)鎖

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%AE%B2%E4%B8%80%E4%B8%8Bmysql%E9%87%8C%E6%9C%89%E5%93%AA%E4%BA%9B%E9%94%81)講一下 mysql 裏有哪些鎖？

在 MySQL 裏，根據加鎖的範圍，可以分為**全局鎖、表級鎖和行鎖**三類。
![[Pasted image 20250116161647.png]]

- **全局鎖**：通過 flush tables with read lock 語句會將整個數據庫就處於隻讀狀态了，這時其他線程執行以下操作，增删改或者表結構修改都會阻塞。全局鎖主要應用於做**全庫邏輯備份**，這樣在備份數據庫期間，不會因為數據或表結構的更新，而出現備份文件的數據與預期的不一樣。
    
- **表級鎖**：MySQL 裏面表級别的鎖有這幾種：
    
    - 表鎖：通過 lock tables 語句可以對表加表鎖，表鎖除了會限制别的線程的讀寫外，也會限制本線程接下來的讀寫操作。
        
    - 元數據鎖：當我們對數據庫表進行操作時，會自動給這個表加上 MDL，對一張表進行 CRUD 操作時，加的是 **MDL 讀鎖**；對一張表做結構變更操作的時候，加的是 **MDL 寫鎖**；MDL 是為了保證當用戶對表執行 CRUD 操作時，防止其他線程對這個表結構做了變更。
        
    - 意向鎖：當執行插入、更新、删除操作，需要先對表加上「意向獨占鎖」，然後對該記錄加獨占鎖。**意向鎖的目的是為了快速判斷表裏是否有記錄被加鎖**。
        
- **行級鎖**：InnoDB 引擎是支持行級鎖的，而 MyISAM 引擎并不支持行級鎖。
    
- 記錄鎖，鎖住的是一條記錄。而且記錄鎖是有 S 鎖和 X 鎖之分的，滿足讀寫互斥，寫寫互斥
    
- 間隙鎖，隻存在於可重複讀隔離級别，目的是為了解決可重複讀隔離級别下幻讀的現象。
    
- Next-Key Lock 稱為臨鍵鎖，是 Record Lock + Gap Lock 的組合，鎖定一個範圍，并且鎖定記錄本身。
    

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E8%A1%A8%E9%94%81%E5%92%8C%E8%A1%8C%E9%94%81%E6%9C%89%E4%BB%80%E4%B9%88%E4%BD%9C%E7%94%A8)數據庫的表鎖和行鎖有什麽作用？

表鎖的作用：

- **整體控制**：表鎖可以用來控制整個表的并發訪問，當一個事務獲取了表鎖時，其他事務無法對該表進行任何讀寫操作，從而确保數據的完整性和一致性。
- **粒度大**：表鎖的粒度比較大，在鎖定表的情況下，可能會影響到整個表的其他操作，可能會引起鎖競争和性能問題。
- **适用於大批量操作**：表鎖适合於需要大批量操作表中數據的場景，例如表的重建、大量數據的加載等。

行鎖的作用：

- **細粒度控制**：行鎖可以精确控制對表中某行數據的訪問，使得其他事務可以同時訪問表中的其他行數據，在并發量大的系統中能夠提高并發性能。
- **減少鎖沖突**：行鎖不會像表鎖那樣造成整個表的鎖沖突，減少了鎖競争的可能性，提高了并發訪問的效率。
- **适用於頻繁單行操作**：行鎖适合於需要頻繁對表中單獨行進行操作的場景，例如訂單系統中的訂單修改、删除等操作。

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E4%B8%A4%E4%B8%AA%E7%BA%BF%E7%A8%8B%E7%9A%84update%E8%AF%AD%E5%8F%A5%E5%90%8C%E6%97%B6%E5%A4%84%E7%90%86%E4%B8%80%E6%9D%A1%E6%95%B0%E6%8D%AE-%E4%BC%9A%E4%B8%8D%E4%BC%9A%E6%9C%89%E9%98%BB%E5%A1%9E)MySQL 兩個線程的 update 語句同時處理一條數據，會不會有阻塞？

如果是兩個事務同時更新了 id = 1，比如 update ... where id = 1，那麽是會阻塞的。因為 InnoDB 存儲引擎實現了行級鎖。

當 A 事務對 id =1 這行記錄進行更新時，會對主鍵 id 為 1 的記錄加 X 類型的記錄鎖，這樣第二事務對 id = 1 進行更新時，發現已經有記錄鎖了，就會陷入阻塞狀态。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%B8%A4%E6%9D%A1update%E8%AF%AD%E5%8F%A5%E5%A4%84%E7%90%86%E4%B8%80%E5%BC%A0%E8%A1%A8%E7%9A%84%E4%B8%8D%E5%90%8C%E7%9A%84%E4%B8%BB%E9%94%AE%E8%8C%83%E5%9B%B4%E7%9A%84%E8%AE%B0%E5%BD%95-%E4%B8%80%E4%B8%AA-10-%E4%B8%80%E4%B8%AA-15-%E4%BC%9A%E4%B8%8D%E4%BC%9A%E9%81%87%E5%88%B0%E9%98%BB%E5%A1%9E-%E5%BA%95%E5%B1%82%E6%98%AF%E4%B8%BA%E4%BB%80%E4%B9%88%E7%9A%84)兩條 update 語句處理一張表的不同的主鍵範圍的記錄，一個 <10，一個> 15，會不會遇到阻塞？底層是為什麽的？

不會，因為鎖住的範圍不一樣，不會形成沖突。

- 第一條 update sql 的話（ id<10），鎖住的範圍是（-♾️，10）
- 第二條 update sql 的話（id >15），鎖住的範圍是（15，+♾️）

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%A6%82%E6%9E%9C2%E4%B8%AA%E8%8C%83%E5%9B%B4%E4%B8%8D%E6%98%AF%E4%B8%BB%E9%94%AE%E6%88%96%E7%B4%A2%E5%BC%95-%E8%BF%98%E4%BC%9A%E9%98%BB%E5%A1%9E%E5%90%97)如果 2 個範圍不是主鍵或索引？還會阻塞嗎？

如果 2 個範圍查詢的字段不是索引的話，那就代表 update 沒有用到索引，這時候觸發了全表掃描，全部索引都會加行級鎖，這時候第二條 update 執行的時候，就會阻塞了。

因為如果 update 沒有用到索引，在掃描過程中會對索引加鎖，所以全表掃描的場景下，所有記錄都會被加鎖，也就是這條 update 語句産生了 4 個記錄鎖和 5 個間隙鎖，相當於鎖住了全表。

![[100_attachements/62c87b5ba29281f7a6af61697f4af6cd_MD5.png]]

## [#](https://xiaolincoding.com/interview/mysql.html#%E6%97%A5%E5%BF%97)日志

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6%E6%98%AF%E5%88%86%E6%88%90%E4%BA%86%E5%93%AA%E5%87%A0%E7%A7%8D)日志文件是分成了哪幾種？

- redo log 重做日志，是 Innodb 存儲引擎層生成的日志，實現了事務中的**持久性**，主要**用於掉電等故障恢複**；
- undo log 回滾日志，是 Innodb 存儲引擎層生成的日志，實現了事務中的**原子性**，主要**用於事務回滾和 MVCC**。
- bin log 二進制日志，是 Server 層生成的日志，主要**用於數據備份和主從複制**；
- relay log 中繼日志，用於主從複制場景下，slave 通過 io 線程拷貝 master 的 bin log 後本地生成的日志
- 慢查詢日志，用於記錄執行時間過長的 sql，需要設置阈值後手動開啓

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%AE%B2%E4%B8%80%E4%B8%8Bbinlog)講一下 binlog

MySQL 在完成一條更新操作後，Server 層還會生成一條 binlog，等之後事務提交的時候，會將該事物執行過程中産生的所有 binlog 統一寫 入 binlog 文件，binlog 是 MySQL 的 Server 層實現的日志，所有存儲引擎都可以使用。

binlog 是追加寫，寫滿一個文件，就創建一個新的文件繼續寫，不會覆蓋以前的日志，保存的是全量的日志，用於備份恢複、主從複制；

binlog 文件是記錄了所有數據庫表結構變更和表數據修改的日志，不會記錄查詢類的操作，比如 SELECT 和 SHOW 操作。

binlog 有 3 種格式類型，分别是 STATEMENT（默認格式）、ROW、 MIXED，區别如下：

- STATEMENT：每一條修改數據的 SQL 都會被記錄到 binlog 中（相當於記錄了邏輯操作，所以針對這種格式， binlog 可以稱為邏輯日志），主從複制中 slave 端再根據 SQL 語句重現。但 STATEMENT 有動态函數的問題，比如你用了 uuid 或者 now 這些函數，你在主庫上執行的結果并不是你在從庫執行的結果，這種随時在變的函數會導致複制的數據不一致；
- ROW：記錄行數據最終被修改成什麽樣了（這種格式的日志，就不能稱為邏輯日志了），不會出現 STATEMENT 下動态函數的問題。但 ROW 的缺點是每行數據的變化結果都會被記錄，比如執行批量 update 語句，更新多少行數據就會産生多少條記錄，使 binlog 文件過大，而在 STATEMENT 格式下隻會記錄一個 update 語句而已；
- MIXED：包含了 STATEMENT 和 ROW 模式，它會根據不同的情況自動使用 ROW 模式和 STATEMENT 模式；

### [#](https://xiaolincoding.com/interview/mysql.html#undolog%E6%97%A5%E5%BF%97%E7%9A%84%E4%BD%9C%E7%94%A8%E6%98%AF%E4%BB%80%E4%B9%88)UndoLog 日志的作用是什麽？

undo log 是一種用於撤銷回退的日志，**它保證了事務的** **ACID 特性中的原子性**（Atomicity）。

在事務沒提交之前，MySQL 會先記錄更新前的數據到 undo log 日志文件裏面，當事務回滾時，可以利用 undo log 來進行回滾。如下圖：
![[Pasted image 20250116161710.png]]

每當 InnoDB 引擎對一條記錄進行操作（修改、删除、新增）時，要把回滾時需要的信息都記錄到 undo log 裏，比如：

- 在**插入**一條記錄時，要把這條記錄的主鍵值記下來，這樣之後回滾時隻需要把這個主鍵值對應的記錄**删掉**就好了；
- 在**删除**一條記錄時，要把這條記錄中的内容都記下來，這樣之後回滾時再把由這些内容組成的記錄**插入**到表中就好了；
- 在**更新**一條記錄時，要把被更新的列的舊值記下來，這樣之後回滾時再把這些列**更新為舊值**就好了。

在發生回滾時，就讀取 undo log 裏的數據，然後做原先相反操作。比如當 delete 一條記錄時，undo log 中會把記錄中的内容都記下來，然後執行回滾操作的時候，就讀取 undo log 裏的數據，然後進行 insert 操作。

### [#](https://xiaolincoding.com/interview/mysql.html#%E6%9C%89%E4%BA%86undolog%E4%B8%BA%E5%95%A5%E8%BF%98%E9%9C%80%E8%A6%81redolog%E5%91%A2)有了 undolog 為啥還需要 redolog 呢？

Buffer Pool 是提高了讀寫效率沒錯，但是問題來了，Buffer Pool 是基於内存的，而内存總是不可靠，萬一斷電重啓，還沒來得及落盤的髒頁數據就會丢失。

為了防止斷電導致數據丢失的問題，當有一條記錄需要更新的時候，InnoDB 引擎就會先更新内存（同時標記為髒頁），然後將本次對這個頁的修改以 redo log 的形式記錄下來，**這個時候更新就算完成了**。

後續，InnoDB 引擎會在适當的時候，由後台線程將緩存在 Buffer Pool 的髒頁刷新到磁盤裏，這就是 **WAL （Write-Ahead Logging）技術**。

**WAL 技術指的是， MySQL 的寫操作并不是立刻寫到磁盤上，而是先寫日志，然後在合适的時間再寫到磁盤上**。

過程如下圖：

![[100_attachements/392060f8c0c077a6fe6159467f4ce101_MD5.png]]

redo log 是物理日志，記錄了某個數據頁做了什麽修改，比如**對 XXX 表空間中的 YYY 數據頁 ZZZ 偏移量的地方做了 AAA 更新**，每當執行一個事務就會産生這樣的一條或者多條物理日志。

在事務提交時，隻要先將 redo log 持久化到磁盤即可，可以不需要等到將緩存在 Buffer Pool 裏的髒頁數據持久化到磁盤。

當系統崩潰時，雖然髒頁數據沒有持久化，但是 redo log 已經持久化，接着 MySQL 重啓後，可以根據 redo log 的内容，將所有數據恢複到最新的狀态。

redo log 和 undo log 這兩種日志是屬於 InnoDB 存儲引擎的日志，它們的區别在於：

- redo log 記錄了此次事務「**完成後**」的數據狀态，記錄的是更新**之後**的值；
- undo log 記錄了此次事務「**開始前**」的數據狀态，記錄的是更新**之前**的值；

事務提交之前發生了崩潰，重啓後會通過 undo log 回滾事務，事務提交之後發生了崩潰，重啓後會通過 redo log 恢複事務，如下圖：

![[Pasted image 20250116161722.png]]

所以有了 redo log，再通過 WAL 技術，InnoDB 就可以保證即使數據庫發生異常重啓，之前已提交的記錄都不會丢失，這個能力稱為 **crash-safe**（崩潰恢複）。可以看出來， **redo log 保證了事務四大特性中的持久性**。

寫入 redo log 的方式使用了追加操作， 所以磁盤操作是**順序寫**，而寫入數據需要先找到寫入位置，然後才寫到磁盤，所以磁盤操作是**随機寫**。

磁盤的「順序寫 」比「随機寫」 高效的多，因此 redo log 寫入磁盤的開銷更小。

針對「順序寫」為什麽比「随機寫」更快這個問題，可以比喻為你有一個本子，按照順序一頁一頁寫肯定比寫一個字都要找到對應頁寫快得多。

可以說這是 WAL 技術的另外一個優點：**MySQL 的寫操作從磁盤的「随機寫」變成了「順序寫」**，提升語句的執行性能。這是因為 MySQL 的寫操作并不是立刻更新到磁盤上，而是先記錄在日志上，然後在合适的時間再更新到磁盤上 。

至此， 針對為什麽需要 redo log 這個問題我們有兩個答案：

- **實現事務的持久性，讓 MySQL 有 crash-safe 的能力**，能夠保證 MySQL 在任何時間段突然崩潰，重啓後之前已提交的記錄都不會丢失；
- **將寫操作從「随機寫」變成了「順序寫」**，提升 MySQL 寫入磁盤的性能。

### [#](https://xiaolincoding.com/interview/mysql.html#redo-log%E6%80%8E%E4%B9%88%E4%BF%9D%E8%AF%81%E6%8C%81%E4%B9%85%E6%80%A7%E7%9A%84)redo log 怎麽保證持久性的？

Redo log 是 MySQL 中用於保證持久性的重要機制之一。它通過以下方式來保證持久性：

1. Write-ahead logging（WAL）：在事務提交之前，將事務所做的修改操作記錄到 redo log 中，然後再將數據寫入磁盤。這樣即使在數據寫入磁盤之前發生了宕機，系統可以通過 redo log 中的記錄來恢複數據。
2. Redo log 的順序寫入：redo log 采用追加寫入的方式，將 redo 日志記錄追加到文件末尾，而不是随機寫入。這樣可以減少磁盤的随機 I/O 操作，提高寫入性能。
3. Checkpoint 機制：MySQL 會定期將内存中的數據刷新到磁盤，同時將最新的 LSN（Log Sequence Number）記錄到磁盤中，這個 LSN 可以确保 redo log 中的操作是按順序執行的。在恢複數據時，系統會根據 LSN 來确定從哪個位置開始應用 redo log。

### [#](https://xiaolincoding.com/interview/mysql.html#%E8%83%BD%E4%B8%8D%E8%83%BD%E5%8F%AA%E7%94%A8binlog%E4%B8%8D%E7%94%A8relo-log)能不能隻用 binlog 不用 relo log？

不行，binlog 是 server 層的日志，沒辦法記錄哪些髒頁還沒有刷盤，redolog 是存儲引擎層的日志，可以記錄哪些髒頁還沒有刷盤，這樣崩潰恢複的時候，就能恢複那些還沒有被刷盤的髒頁數據。

### [#](https://xiaolincoding.com/interview/mysql.html#binlog-%E4%B8%A4%E9%98%B6%E6%AE%B5%E6%8F%90%E4%BA%A4%E8%BF%87%E7%A8%8B%E6%98%AF%E6%80%8E%E4%B9%88%E6%A0%B7%E7%9A%84)binlog 兩階段提交過程是怎麽樣的？

事務提交後，redo log 和 binlog 都要持久化到磁盤，但是這兩個是獨立的邏輯，可能出現半成功的狀态，這樣就造成兩份日志之間的邏輯不一致。

在 MySQL 的 InnoDB 存儲引擎中，開啓 binlog 的情況下，MySQL 會同時維護 binlog 日志與 InnoDB 的 redo log，為了保證這兩個日志的一致性，MySQL 使用了**内部 XA 事務**（是的，也有外部 XA 事務，跟本文不態相關，我就不介紹了），内部 XA 事務由 binlog 作為協調者，存儲引擎是參與者。

當客戶端執行 commit 語句或者在自動提交的情況下，MySQL 内部開啓一個 XA 事務，**分兩階段來完成 XA 事務的提交**，如下圖：
![[Pasted image 20250116161732.png]]
![[100_attachements/585e976cad87d7be51506661f779fe28_MD5.webp]]

從圖中可看出，事務的提交過程有兩個階段，就是**將 redo log 的寫入拆成了兩個步驟：prepare 和 commit，中間再穿插寫入 binlog**，具體如下：

- **prepare 階段**：將 XID（内部 XA 事務的 ID） 寫入到 redo log，同時將 redo log 對應的事務狀态設置為 prepare，然後將 redo log 持久化到磁盤（innodb_flush_log_at_trx_commit = 1 的作用）；
- **commit 階段**：把 XID 寫入到 binlog，然後將 binlog 持久化到磁盤（sync_binlog = 1 的作用），接着調用引擎的提交事務接口，將 redo log 狀态設置為 commit，此時該狀态并不需要持久化到磁盤，隻需要 write 到文件系統的 page cache 中就夠了，因為隻要 binlog 寫磁盤成功，就算 redo log 的狀态還是 prepare 也沒有關系，一樣會被認為事務已經執行成功；

我們來看看在兩階段提交的不同時刻，MySQL 異常重啓會出現什麽現象？下圖中有時刻 A 和時刻 B 都有可能發生崩潰：

![[100_attachements/3571d07254f80e376319f2c405186c62_MD5.png]]

不管是時刻 A（redo log 已經寫入磁盤， binlog 還沒寫入磁盤），還是時刻 B （redo log 和 binlog 都已經寫入磁盤，還沒寫入 commit 標識）崩潰，**此時的 redo log 都處於 prepare 狀态**。

在 MySQL 重啓後會按順序掃描 redo log 文件，碰到處於 prepare 狀态的 redo log，就拿着 redo log 中的 XID 去 binlog 查看是否存在此 XID：

- **如果 binlog 中沒有當前内部 XA 事務的 XID，說明 redolog 完成刷盤，但是 binlog 還沒有刷盤，則回滾事務**。對應時刻 A 崩潰恢複的情況。
- **如果 binlog 中有當前内部 XA 事務的 XID，說明 redolog 和 binlog 都已經完成了刷盤，則提交事務**。對應時刻 B 崩潰恢複的情況。

可以看到，**對於處於 prepare 階段的 redo log，即可以提交事務，也可以回滾事務，這取決於是否能在 binlog 中查找到與 redo log 相同的 XID**，如果有就提交事務，如果沒有就回滾事務。這樣就可以保證 redo log 和 binlog 這兩份日志的一致性了。

所以說，**兩階段提交是以 binlog 寫成功為事務提交成功的標識**，因為 binlog 寫成功了，就意味着能在 binlog 中查找到與 redo log 相同的 XID。

### [#](https://xiaolincoding.com/interview/mysql.html#update%E8%AF%AD%E5%8F%A5%E7%9A%84%E5%85%B7%E4%BD%93%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B%E6%98%AF%E6%80%8E%E6%A0%B7%E7%9A%84)update 語句的具體執行過程是怎樣的？

具體更新一條記錄 `UPDATE t_user SET name = 'xiaolin' WHERE id = 1;` 的流程如下:

1. 執行器負責具體執行，會調用存儲引擎的接口，通過主鍵索引樹搜索獲取 id = 1 這一行記錄：
    - 如果 id=1 這一行所在的數據頁本來就在 buffer pool 中，就直接返回給執行器更新；
    - 如果記錄不在 buffer pool，將數據頁從磁盤讀入到 buffer pool，返回記錄給執行器。
2. 執行器得到聚簇索引記錄後，會看一下更新前的記錄和更新後的記錄是否一樣：
    - 如果一樣的話就不進行後續更新流程；
    - 如果不一樣的話就把更新前的記錄和更新後的記錄都當作參數傳給 InnoDB 層，讓 InnoDB 真正的執行更新記錄的操作；
3. 開啓事務， InnoDB 層更新記錄前，首先要記錄相應的 undo log，因為這是更新操作，需要把被更新的列的舊值記下來，也就是要生成一條 undo log，undo log 會寫入 Buffer Pool 中的 Undo 頁面，不過在内存修改該 Undo 頁面後，需要記錄對應的 redo log。
4. InnoDB 層開始更新記錄，會先更新内存（同時標記為髒頁），然後將記錄寫到 redo log 裏面，這個時候更新就算完成了。為了減少磁盤 I/O，不會立即將髒頁寫入磁盤，後續由後台線程選擇一個合适的時機將髒頁寫入到磁盤。這就是 **WAL 技術**，MySQL 的寫操作并不是立刻寫到磁盤上，而是先寫 redo 日志，然後在合适的時間再將修改的行數據寫到磁盤上。
5. 至此，一條記錄更新完了。
6. 在一條更新語句執行完成後，然後開始記錄該語句對應的 binlog，此時記錄的 binlog 會被保存到 binlog cache，并沒有刷新到硬盤上的 binlog 文件，在事務提交時才會統一將該事務運行過程中的所有 binlog 刷新到硬盤。
7. 事務提交（為了方便說明，這裏不說組提交的過程，隻說兩階段提交）：
    - **prepare 階段**：將 redo log 對應的事務狀态設置為 prepare，然後將 redo log 刷新到硬盤；
    - **commit 階段**：將 binlog 刷新到磁盤，接着調用引擎的提交事務接口，將 redo log 狀态設置為 commit（將事務設置為 commit 狀态後，刷入到磁盤 redo log 文件）；
8. 至此，一條更新語句執行完成。

## [#](https://xiaolincoding.com/interview/mysql.html#%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98)性能調優

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E7%9A%84explain%E6%9C%89%E4%BB%80%E4%B9%88%E4%BD%9C%E7%94%A8)mysql 的 explain 有什麽作用？

explain 是查看 sql 的執行計劃，主要用來分析 sql 語句的執行過程，比如有沒有走索引，有沒有外部排序，有沒有索引覆蓋等等。

如下圖，就是一個沒有使用索引，并且是一個全表掃描的查詢語句。
![[Pasted image 20250116161741.png]]

對於執行計劃，參數有：

- possible_keys 字段表示可能用到的索引；
- key 字段表示實際用的索引，如果這一項為 NULL，說明沒有使用索引；
- key_len 表示索引的長度；
- rows 表示掃描的數據行數。
- type 表示數據掃描類型，我們需要重點看這個。

type 字段就是描述了找到所需數據時使用的掃描方式是什麽，常見掃描類型的**執行效率從低到高的順序為**：

- All（全表掃描）：在這些情況裏，all 是最壞的情況，因為采用了全表掃描的方式。
- index（全索引掃描）：index 和 all 差不多，隻不過 index 對索引表進行全掃描，這樣做的好處是不再需要對數據進行排序，但是開銷依然很大。所以，要盡量避免全表掃描和全索引掃描。
- range（索引範圍掃描）：range 表示采用了索引範圍掃描，一般在 where 子句中使用 <、>、in、between 等關鍵詞，隻檢索給定範圍的行，屬於範圍查找。**從這一級别開始，索引的作用會越來越明顯，因此我們需要盡量讓 SQL 查詢可以使用到 range 這一級别及以上的 type 訪問方式**。
- ref（非唯一索引掃描）：ref 類型表示采用了非唯一索引，或者是唯一索引的非唯一性前綴，返回數據返回可能是多條。因為雖然使用了索引，但該索引列的值并不唯一，有重複。這樣即使使用索引快速查找到了第一條數據，仍然不能停止，要進行目標值附近的小範圍掃描。但它的好處是它并不需要掃全表，因為索引是有序的，即便有重複值，也是在一個非常小的範圍内掃描。
- eq_ref（唯一索引掃描）：eq_ref 類型是使用主鍵或唯一索引時産生的訪問方式，通常使用在多表聯查中。比如，對兩張表進行聯查，關聯條件是兩張表的 user_id 相等，且 user_id 是唯一索引，那麽使用 EXPLAIN 進行執行計劃查看的時候，type 就會顯示 eq_ref。
- const（結果隻有一條的主鍵或唯一索引掃描）：const 類型表示使用了主鍵或者唯一索引與常量值進行比較，比如 select name from product where id=1。需要說明的是 const 類型和 eq_ref 都使用了主鍵或唯一索引，不過這兩個類型有所區别，**const 是與常量進行比較，查詢效率會更快，而 eq_ref 通常用於多表聯查中**。

extra 顯示的結果，這裏說幾個重要的參考指標：

- Using filesort ：當查詢語句中包含 group by 操作，而且無法利用索引完成排序操作的時候， 這時不得不選擇相應的排序算法進行，甚至可能會通過文件排序，效率是很低的，所以要避免這種問題的出現。
- Using temporary：使了用臨時表保存中間結果，MySQL 在對查詢結果排序時使用臨時表，常見於排序 order by 和分組查詢 group by。效率低，要避免這種問題的出現。
- Using index：所需數據隻需在索引即可全部獲得，不須要再到表中取數據，也就是使用了覆蓋索引，避免了回表操作，效率不錯。

### [#](https://xiaolincoding.com/interview/mysql.html#%E7%BB%99%E4%BD%A0%E5%BC%A0%E8%A1%A8-%E5%8F%91%E7%8E%B0%E6%9F%A5%E8%AF%A2%E9%80%9F%E5%BA%A6%E5%BE%88%E6%85%A2-%E4%BD%A0%E6%9C%89%E9%82%A3%E4%BA%9B%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88)給你張表，發現查詢速度很慢，你有那些解決方案

- **分析查詢語句**：使用 EXPLAIN 命令分析 SQL 執行計劃，找出慢查詢的原因，比如是否使用了全表掃描，是否存在索引未被利用的情況等，并根據相應情況對索引進行适當修改。
- **創建或優化索引**：根據查詢條件創建合适的索引，特别是經常用於 WHERE 子句的字段、Orderby 排序的字段、Join 連表查詢的字典、 group by 的字段，并且如果查詢中經常涉及多個字段，考慮創建聯合索引，使用聯合索引要符合最左匹配原則，不然會索引失效
- ** 避免索引失效：** 比如不要用左模糊匹配、函數計算、表達式計算等等。
- **查詢優化**：避免使用 SELECT *，隻查詢真正需要的列；使用覆蓋索引，即索引包含所有查詢的字段；聯表查詢最好要以小表驅動大表，并且被驅動表的字段要有索引，當然最好通過冗餘字段的設計，避免聯表查詢。
- ** 分頁優化：** 針對 limit n,y 深分頁的查詢優化，可以把 Limit 查詢轉換成某個位置的查詢：select * from tb_sku where id>20000 limit 10，該方案适用於主鍵自增的表，
- **優化數據庫表**：如果單表的數據超過了千萬級别，考慮是否需要將大表拆分為小表，減輕單個表的查詢壓力。也可以將字段多的表分解成多個表，有些字段使用頻率高，有些低，數據量大時，會由於使用頻率低的存在而變慢，可以考慮分開。
- **使用緩存技術**：引入緩存層，如 Redis，存儲熱點數據和頻繁查詢的結果，但是要考慮緩存一致性的問題，對於讀請求會選擇旁路緩存策略，對於寫請求會選擇先更新 db，再删除緩存的策略。

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%A6%82%E6%9E%9Cexplain%E7%94%A8%E5%88%B0%E7%9A%84%E7%B4%A2%E5%BC%95%E4%B8%8D%E6%AD%A3%E7%A1%AE%E7%9A%84%E8%AF%9D-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8A%9E%E6%B3%95%E5%B9%B2%E9%A2%84%E5%90%97)如果 Explain 用到的索引不正确的話，有什麽辦法幹預嗎？

可以使用 force index，強制走索引。

比如：

```
EXPLAIN SELECT 
    productName, buyPrice
FROM
    products 
FORCE INDEX (idx_buyprice)
WHERE
    buyPrice BETWEEN 10 AND 80
ORDER BY buyPrice; 
```

輸出：
![[Pasted image 20250116161751.png]]

## [#](https://xiaolincoding.com/interview/mysql.html#%E6%9E%B6%E6%9E%84)架構

### [#](https://xiaolincoding.com/interview/mysql.html#mysql%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6%E4%BA%86%E8%A7%A3%E5%90%97)MySQL 主從複制了解嗎

MySQL 的主從複制依賴於 binlog ，也就是記錄 MySQL 上的所有變化并以二進制形式保存在磁盤上。複制的過程就是將 binlog 中的數據從主庫傳輸到從庫上。

這個過程一般是**異步**的，也就是主庫上執行事務操作的線程不會等待複制 binlog 的線程同步完成。
![[Pasted image 20250116161801.png]]

MySQL 集群的主從複制過程梳理成 3 個階段：

- **寫入 Binlog**：主庫寫 binlog 日志，提交事務，并更新本地存儲數據。
- **同步 Binlog**：把 binlog 複制到所有從庫上，每個從庫把 binlog 寫到暫存日志中。
- **回放 Binlog**：回放 binlog，并更新存儲引擎中的數據。

具體詳細過程如下：

- MySQL 主庫在收到客戶端提交事務的請求之後，會先寫入 binlog，再提交事務，更新存儲引擎中的數據，事務提交完成後，返回給客戶端 “操作成功” 的響應。
- 從庫會創建一個專門的 I/O 線程，連接主庫的 log dump 線程，來接收主庫的 binlog 日志，再把 binlog 信息寫入 relay log 的中繼日志裏，再返回給主庫 “複制成功” 的響應。
- 從庫會創建一個用於回放 binlog 的線程，去讀 relay log 中繼日志，然後回放 binlog 更新存儲引擎中的數據，最終實現主從的數據一致性。

在完成主從複制之後，你就可以在寫數據時隻寫主庫，在讀數據時隻讀從庫，這樣即使寫請求會鎖表或者鎖記錄，也不會影響讀請求的執行。

### [#](https://xiaolincoding.com/interview/mysql.html#%E4%B8%BB%E4%BB%8E%E5%BB%B6%E8%BF%9F%E9%83%BD%E6%9C%89%E4%BB%80%E4%B9%88%E5%A4%84%E7%90%86%E6%96%B9%E6%B3%95)主從延遲都有什麽處理方法？

**強制走主庫方案**：對於大事務或資源密集型操作，直接在主庫上執行，避免從庫的額外延遲。

### [#](https://xiaolincoding.com/interview/mysql.html#%E5%88%86%E8%A1%A8%E5%92%8C%E5%88%86%E5%BA%93%E6%98%AF%E4%BB%80%E4%B9%88-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)分表和分庫是什麽？有什麽區别？


- **分庫**是一種水平擴展數據庫的技術，將數據根據一定規則劃分到多個獨立的數據庫中。每個數據庫隻負責存儲部分數據，實現了數據的拆分和分布式存儲。分庫主要是為了解決并發連接過多，單機 mysql 扛不住的問題。
- **分表**指的是將單個數據庫中的表拆分成多個表，每個表隻負責存儲一部分數據。這種數據的垂直劃分能夠提高查詢效率，減輕單個表的壓力。分表主要是為了解決單表數據量態大，導致查詢性能下降的問題。

分庫與分表可以從：垂直（縱向）和 水平（橫向）兩種緯度進行拆分。下邊我們以經典的訂單業務舉例，看看如何拆分。
![[Pasted image 20250116161817.png]]

- **垂直分庫**：一般來說按照業務和功能的維度進行拆分，將不同業務數據分别放到不同的數據庫中，核心理念 專庫專用。按業務類型對數據分離，剝離為多個數據庫，像訂單、支付、會員、積分相關等表放在對應的訂單庫、支付庫、會員庫、積分庫。垂直分庫把一個庫的壓力分攤到多個庫，提升了一些數據庫性能，但并沒有解決由於單表數據量過大導致的性能問題，所以就需要配合後邊的分表來解決。
- **垂直分表**：針對業務上字段比較多的大表進行的，一般是把業務寬表中比較獨立的字段，或者不常用的字段拆分到單獨的數據表中，是一種大表拆小表的模式。數據庫它是以行為單位將數據加載到内存中，這樣拆分以後核心表大多是訪問頻率較高的字段，而且字段長度也都較短，因而可以加載更多數據到内存中，減少磁盤 IO，增加索引查詢的命中率，進一步提升數據庫性能。
- **水平分庫**：是把同一個表按一定規則拆分到不同的數據庫中，每個庫可以位於不同的服務器上，以此實現水平擴展，是一種常見的提升數據庫性能的方式。這種方案往往能解決單庫存儲量及性能瓶頸問題，但由於同一個表被分配在不同的數據庫中，數據的訪問需要額外的路由工作，因此系統的複雜度也被提升了。
- **水平分表**：是在**同一個數據庫内**，把一張大數據量的表按一定規則，切分成多個結構完全相同表，而每個表隻存原表的一部分數據。水平分表盡管拆分了表，但子表都還是在同一個數據庫實例中，隻是解決了單一表數據量過大的問題，并沒有將拆分後的表分散到不同的機器上，還在競争同一個物理機的 CPU、内存、網絡 IO 等。要想進一步提升性能，就需要將拆分後的表分散到不同的數據庫中，達到分布式的效果。