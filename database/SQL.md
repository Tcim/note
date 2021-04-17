### 计算字段
#### 字符串拼接
```sql
SELECT Concat( column_1, 'str_1', RTRIM(column_2), 'str_2' ...) AS new_field_name from table;
# Concat 拼接函数
# RTRIM 删除右侧列宽填充空格
# LTRIM 删除字符串左边空格
# TRIM 删除字符串两边空格
# AS 字段别名
```
#### 算数运算
```sql
SELECT id, name, score_1 + score_2 AS sumScore from table;
# 支持 +, -, *, / 操作
```
### 函数处理字段
#### 字符串/文本函数
  - `LEFT()`: 返回字符串左边的字符
  - `LENGTH()`/`LEN()`: 字符串长度
  - `LOWER()`: 转小写字母
  - `UPPER()`: 转大写字母
  - `LTRIM()`/`RTRIM()`/`TRIM()`: 处理右边/左边/两边空格
  - `SOUNDEX()`: 字符串的soundex值(将任何文本转换为描述其语音表示的字母数字模式)
#### 时间和日期处理函数
  - `Year()`/`Month()`/`DAY()`: 得到年份/月份/日期
  - `to_date('01-01-2021')`/`to_char('01-01-2021', 'YYYY-MM-DD')`: 日期与字符串转换
#### 数值处理函数
  - `ABS()`/`COS()`/`EXP()`/`PI()`/`SIN()`/`SQRT()`/`TAN()`
### 数据聚集
#### 聚集函数
  - `AVG()`: 平均值
  - `COUNT()`: 计数函数
    - `COUNT(*)`: 总行数
    - `COUNT(column_name)`: 总行数, 忽略指定列中值为NULL的行
  - `MAX(column_name)`: 最大值, 忽略NULL值
  - `MIN(column_name)`: 最小值, 忽略NULL值
  - `SUM(column_name)`: 和值, 忽略NULL值
#### `DISTINCT`/`ALL`关键字
`DISTINCT`用在聚集函数参数的列前, 忽略列中的重复值; `ALL`为默认, 可省略, 计算所有值.
```sql
SELECT AVG(DISTINCT column_name) AS avgName FROM table WHERE ...;
# 计算时, column_name列中重复的值只计算一次
```
#### 组合聚集函数
```sql
SELECT COUNT(*) AS nums, MIN(column_1) AS minValue, MAX(column_2) AS maxValue
FROM table
WHERE ...;
```
### 数据分组
#### `GROUP BY` 子句
  - `GROUP BY`子句可以包含任意数目的列, 可以对分组实现嵌套
  - 如果使用了嵌套分组, 数据将在最后指定的分组上汇总
  - 子句中每一列都必须时检索列或有效的表达式(不能时聚集函数), 表达式必须与`SELECT`中表达式相同, 且不能使用别名
  - 多数SQL不允许`GROUP BY`子句中有可变长度的数据类型(文本)
  - 除聚集计算外, `SELECT`中每一列都必须在`GROUP BY`子句中给出
  - NULL值的行将作为单独的分组
  - `GROUP BY`子句必须在`WHERE`子句后, `ORDER BY`子句之前.
如:
```sql
SELECT column_1, column_2, column_3, COUNT(*) as nums
FROM table
GROUP BY column_1, column_2, column_3
```
~~~text
结果如下样式(每行表示一个分组):
```text
column_1 column_2 column_3 nums
a		 b        c        5
a        b        x        3
a        d        c        4
a        d        x        2
...
e        b        c        2
e        b        x        2
e        d        c        3
...
```
~~~
#### 分组过滤
`WHERE`子句用于对行的过滤, `HAVING`子句用于对分组过滤
如:
```sql
SELECT column_1, column_2, column_3, COUNT(*) as nums
FROM table1
GROUP BY column_1, column_2, column_3
HAVING COUNT(*) >= 3
# 输出将不包含nums <=2 的分组
```
### 子查询
#### 用于查询过滤
```sql
SELECT * 
FROM table_1
WHERE column_name IN (
    SELECT column_name
    FROM table_2
    WHERE ...
);
```
**注意:**
  - 子查询的`SELCET`语句只能查询单列
  - 子查询可嵌套, 但影响性能
#### 作为计算字段使用
```sql
SELECT id, name, (
    SELECT COUNT(*)
    FROM table2
    WHERE table1.id = table2.id
) AS counts
FROM table1;
```
### 联结表
关系型数据库中, 需要的数据往往存在于多个表中, 表与表之间通过外键约束, 建立关系. 查询时, 使用联结将表关联起来, 以实现高效的查询.😎
#### 创建联结
```sql
SELECT table1.id, table1.column_1, table1.column_2, table2.column_3
FROM table1, table2
WHERE table1.id = table2.id;
```
上例将table1中的每一行与table2中与其id等于的行联结, `WHERE`子句中的`table1.id = table2.id`为联结限定条件. **若无此限定条件, 则table1中的每一行将于table2中的每一行进行联结**
#### 内联结
内联结即为上述的等值联结, 通过限定相等关系来联结两个表. 如上述的写法外, 还可以用`INNER JOIN ... ON ...`关键字来表示:
```sql
SELECT table1.id, table1.column_1, table1.column_2, table2.column_3
FROM table1 INNER JOIN table2 ON table1.id = table2.id;
```
#### 自联结
自联结是在某些情况下表需要与自己联结产生需要的结果, 当在`SELECT`语句中不止一次使用一个表时, 需要对表起别名. 如下展示自联结:
 ```sql
SELECT C1.column_1, C1.column_2, C2.column_3 
FROM table1 AS C1, table2 AS C2
WHERE C1.column_4 == C2.column_4
 ```
**自联结通常用来替代需要在同一表中检索数据的子查询语句**

#### 外联结
当使用等值限定的内联结时, 联结结果中只包含两个表中比较值同时存在的数据行, 如table1中存在一行数据id=3, table2中不存在id=3的数据行, 当使用table1.id = table2.id限定进行联结时, 查询结果中不包含id=3的数据行.
许多时候, 我们希望联结查询的结果中包含那些不存在关联行的数据行, 此时可以使用外联结实现.
外连接分为**左外联结**和 **右外联结**, 他们的区别是联结表的顺序不同, 是可以互换使用的.
```sql
SELECT C1.column_1, C2.column_2
FROM table1 AS C1 LEFT OUTER JOIN table2 AS C2 
ON C1.column_0 = C2.column_0
# 等价于
SELECT C1.column_1, C2.column_2
FROM table2 AS C2 RIGHT OUTER JOIN table1 AS C1 
ON C1.column_0 = C2.column_0
```
### 组合查询
组合查询可以认为是对单个查询中`WHERE`子句的分解, DBMS在对组合查询进行处理时会对其进行组合以优化性能. 组合查询使用`UNION`关键字, 如:
```sql
SELECT column_1, column_2, column_3 
FROM table1
WHERE column_1 = ***
    AND column_2 = ****;
# 可以转为组合查询
SELECT column_1, column_2, column_3 
FROM table1
WHERE column_1 = ***
UNION
SELECT column_1, column_2, column_3 
FROM table1
WHERE column_2 = ****
```
**注意:**
  - `UNION`组合查询中不同的`SELECT`语句必有完全相同的列名
  - `UNION`组合查询自动去除了多个查询结果中重复的行, 如果希望保留这些重复行, 使用`UNION ALL`关键字
  - 若需要对组合查询结果进行排序或者分组, 只需要在最后一个`SELECT`语句后加上`ORDER BY`或`GROUP BY`子句
  - 组合查询可以在某种程度上降低`SELECT`语句的复杂度