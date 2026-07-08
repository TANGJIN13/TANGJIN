![[Pasted image 20251119223813.png]]

#SQl注入 QL 查询组件关系详解

让我详细解释这个 SQL 查询中各个组件的关系：

sql

SELECT 1, table_name, 3 FROM information_schema.tables WHERE table_schema = 'test_db'

## 各个组件的作用

### 1. **information_schema.tables**

- 这是 MySQL 的**系统数据库**中的一个**系统表**
    
- 存储了数据库中所有表的**元数据信息**
    
- 包含：表名、数据库名、表类型、存储引擎等信息
    

### 2. **table_schema**

- `information_schema.tables` 表中的一个**列**
    
- 表示**表所属的数据库名称**
    
- 用于过滤特定数据库的表
    

### 3. **table_name**

- `information_schema.tables` 表中的一个**列**
    
- 存储**实际表的名字**
    
- 这是我们想要提取的目标数据
    

### 4. **WHERE table_schema = 'test_db'**

- **条件筛选**：只选择属于 'test_db' 数据库的表
    
- 如果没有这个条件，会返回所有数据库的所有表
    

### 5. **SELECT 1, table_name, 3**

- **字段选择**：选择三个字段作为查询结果
    
- `1` 和 `3` 是**固定值**，用于匹配原查询的字段数量
    
- `table_name` 是我们要获取的**实际数据**
    

## 完整的关系链

text

information_schema.tables (系统表)
    ↓
包含所有数据库的表信息
    ↓
WHERE table_schema = 'test_db' (筛选条件)
    ↓
只保留 'test_db' 数据库的表
    ↓
SELECT table_name (提取表名)
    ↓
获得 'test_db' 数据库中所有表的名称

## 实际数据示例

假设数据库中有这些表：

**information_schema.tables 表中的数据：**

|TABLE_SCHEMA|TABLE_NAME|TABLE_TYPE|
|---|---|---|
|test_db|users|BASE TABLE|
|test_db|posts|BASE TABLE|
|test_db|comments|BASE TABLE|
|mysql|user|BASE TABLE|
|other_db|products|BASE TABLE|

**应用 WHERE 条件后：**

sql

WHERE table_schema = 'test_db'

结果：

|TABLE_SCHEMA|TABLE_NAME|TABLE_TYPE|
|---|---|---|
|test_db|users|BASE TABLE|
|test_db|posts|BASE TABLE|
|test_db|comments|BASE TABLE|

**最终 SELECT 结果：**

sql

SELECT 1, table_name, 3

结果：

|固定值|表名|固定值|
|---|---|---|
|1|users|3|
|1|posts|3|
|1|comments|3|s