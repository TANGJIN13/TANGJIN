MySQL 是一款开源的**关系型数据库管理系统（RDBMS）**，核心用于存储、管理结构化数据（如用户信息、商品数据等），广泛应用于 Web 开发、数据分析等场景。


### 一、注释符（SQL 注释写法）

注释用于说明 SQL 逻辑，MySQL 支持 3 种注释方式：

sql

```sql
-- 1. 单行注释（双连字符 + 空格，最常用）
SELECT * FROM users; -- 这是单行注释，跟在语句后

# 2. 单行注释（井号，类似脚本注释）
# 这是单行注释，单独一行
SELECT username FROM users;

/* 
3. 多行注释（/* 开头，*/ 结尾）
适合注释多行内容
比如复杂 SQL 的逻辑说明
*/
SELECT id, phone FROM users;
```

### 二、增（INSERT：新增数据）

核心：向表中插入一条或多条数据，推荐 **指定字段**（避免字段顺序变动报错）。

#### 语法

sql

```sql
-- 单条插入（推荐）
INSERT INTO 表名 (字段1, 字段2, ...) VALUES (值1, 值2, ...);

-- 批量插入（高效，一次插入多条）
INSERT INTO 表名 (字段1, 字段2, ...) 
VALUES 
(值1-1, 值1-2, ...),
(值2-1, 值2-2, ...),
...;
```

#### 示例（操作 `users` 表）

sql

```sql
-- 单条插入：新增用户张三
INSERT INTO users (username, phone, age, status) 
VALUES ('张三', '13800138000', 22, 1);

-- 批量插入：新增李四、王五
INSERT INTO users (username, phone, age) 
VALUES 
('李四', '13900139000', 25),
('王五', '13700137000', 28);
```

#### 注意

- 字符串值用 **单引号** 包裹（如 `'张三'`），数字 / 布尔值不用（如 `22`、`1`）。
- 有默认值的字段（如 `status` 默认为 1）可省略不写，自动用默认值。

### 三、查（SELECT：查询数据）

核心：从表中获取指定数据，配合 `ORDER BY`（排序）、`LIMIT`（分页）使用频率最高。

#### 基础语法

sql

```sql
SELECT 字段1, 字段2, ... FROM 表名 
[WHERE 条件] -- 可选：筛选数据
[ORDER BY 排序字段1 [ASC/DESC], 排序字段2 ...] -- 可选：排序
[LIMIT 分页参数]; -- 可选：限制返回条数
```

#### 常用示例

sql

```sql
-- 1. 查询所有字段（不推荐，效率低）
SELECT * FROM users;

-- 2. 查询指定字段（推荐）
SELECT id, username, phone FROM users;

-- 3. 条件查询（WHERE）
SELECT * FROM users WHERE age > 20 AND status = 1; -- 年龄>20 且 状态正常
SELECT * FROM users WHERE username LIKE '%张%'; -- 用户名包含“张”（%匹配任意字符）

-- 4. 排序（ORDER BY）：ASC 升序（默认），DESC 降序
SELECT * FROM users ORDER BY create_time DESC; -- 按创建时间倒序（最新在前）
SELECT * FROM users ORDER BY age ASC, id DESC; -- 先按年龄升序，再按 id 倒序

-- 5. 分页（LIMIT）：避免数据过多
SELECT * FROM users LIMIT 0, 10; -- 第 1-10 条（偏移量 0，取 10 条）
SELECT * FROM users LIMIT 10 OFFSET 10; -- 第 11-20 条（等价于 LIMIT 10,10）

-- 6. 组合使用（条件 + 排序 + 分页）
SELECT id, username, age FROM users 
WHERE status = 1 
ORDER BY age DESC 
LIMIT 5; -- 查询状态正常的前 5 个年龄最大的用户
```

### 四、改（UPDATE：修改数据）

核心：更新表中已有数据，**必须加 WHERE 条件**（否则修改全表！）。

#### 语法

sql

```sql
UPDATE 表名 
SET 字段1 = 新值1, 字段2 = 新值2, ... 
WHERE 条件; -- 关键：指定要修改的行
```

#### 示例

sql

```sql
-- 1. 修改单条数据（按主键 id 修改，最安全）
UPDATE users 
SET age = 26, email = 'zhangsan_new@xxx.com' 
WHERE id = 1; -- 只修改 id=1 的用户

-- 2. 修改多条数据（条件明确）
UPDATE users 
SET status = 0 
WHERE create_time < '2025-01-01'; -- 2025年之前的用户禁用
```

#### 注意

- 禁止无 `WHERE` 条件的 `UPDATE`：`UPDATE users SET status = 0;` 会修改所有用户状态，导致数据灾难！
- 字符串新值需用单引号包裹（如 `'zhangsan_new@xxx.com'`）。

### 五、删（DELETE：删除数据）

核心：删除表中数据，**必须加 WHERE 条件**（否则删除全表！）。

#### 语法

sql

```sql
DELETE FROM 表名 
WHERE 条件; -- 关键：指定要删除的行
```

#### 示例

sql

```sql
-- 1. 删除单条数据（按主键 id 删除，最安全）
DELETE FROM users WHERE id = 3; -- 只删除 id=3 的用户

-- 2. 删除多条数据（条件明确）
DELETE FROM users WHERE status = 0 AND age < 18; -- 删除禁用且未成年的用户

-- 3. 清空表（删除所有数据，自增主键重置，高效）
TRUNCATE TABLE users; -- 注意：不可逆，无需 WHERE，慎用！
```

#### 注意

- `DELETE` 是逐行删除，可加 `WHERE` 条件，支持事务回滚；`TRUNCATE` 是直接清空表，不可回滚，速度更快。
- 禁止无 `WHERE` 条件的 `DELETE`：`DELETE FROM users;` 会删除全表数据，慎用！

### 六、核心关键字补充说明

#### 1. ORDER BY（排序）

- 支持多字段排序：先按第一个字段排序，第一个字段值相同时，按第二个字段排序。
- 示例：`ORDER BY age ASC, create_time DESC` → 年龄升序，同年龄按创建时间倒序。
- 不指定 `ASC/DESC` 时，默认是 `ASC`（升序）。

#### 2. LIMIT（分页）

- 语法 1：`LIMIT 偏移量, 条数` → 偏移量从 0 开始（第 1 页：`LIMIT 0,10`，第 2 页：`LIMIT 10,10`）。
- 语法 2：`LIMIT 条数 OFFSET 偏移量` → 与语法 1 等价（`LIMIT 10 OFFSET 10` = `LIMIT 10,10`）。
- 用途：大数据量查询时避免返回过多数据，减轻数据库压力（如列表分页）。

# MySQL 系统表 information_schema的使用

`information_schema` 是 MySQL 内置的**系统数据库**，核心作用是存储整个 MySQL 实例的**元数据**（即数据库、表、字段、索引、权限等的描述信息）。通过查询 `information_schema` 的系统表，你可以无需登录数据库管理工具，直接用 SQL 语句获取 MySQL 的结构信息（比如 “查询所有数据库名”“查看表的字段类型”“统计某表的索引” 等），是运维和开发中必备的工具。


#### 1. SCHEMATA：查询所有数据库信息

存储 MySQL 中所有数据库的基本信息（数据库名、字符集、排序规则等）。

##### 常用查询

sql

```sql
-- 1. 查询所有数据库名（等价于 SHOW DATABASES）
SELECT SCHEMA_NAME AS 数据库名 
FROM information_schema.SCHEMATA;

-- 2. 查询数据库名、字符集、排序规则（常用运维）
SELECT 
  SCHEMA_NAME AS 数据库名,
  DEFAULT_CHARACTER_SET_NAME AS 字符集,
  DEFAULT_COLLATION_NAME AS 排序规则
FROM information_schema.SCHEMATA;

-- 3. 过滤特定数据库（如查询以“test”开头的数据库）
SELECT SCHEMA_NAME AS 数据库名 
FROM information_schema.SCHEMATA 
WHERE SCHEMA_NAME LIKE 'test%';
```

#### 2. TABLES：查询表的基本信息

存储每个数据库下所有表的信息（表名、存储引擎、表类型、创建时间、数据大小等）。

##### 常用查询

sql

```sql
-- 1. 查询指定数据库（如 mydb）的所有表名（等价于 SHOW TABLES FROM mydb）
SELECT TABLE_NAME AS 表名 
FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'mydb'; -- TABLE_SCHEMA 是数据库名

-- 2. 查询表名、存储引擎、创建时间、表注释
SELECT 
  TABLE_NAME AS 表名,
  ENGINE AS 存储引擎, -- InnoDB/MyISAM
  CREATE_TIME AS 创建时间,
  TABLE_COMMENT AS 表注释 -- 创建表时写的 COMMENT
FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'mydb';

-- 3. 统计指定数据库的表数量
SELECT COUNT(*) AS 表总数 
FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'mydb';

-- 4. 查询数据库中所有“用户相关”的表（表名含 user）
SELECT TABLE_NAME AS 表名 
FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'mydb' 
  AND TABLE_NAME LIKE '%user%';
```

#### 3. COLUMNS：查询表的字段详情

存储每个表的字段信息（字段名、数据类型、是否非空、默认值、注释等），最常用的系统表之一（替代 `DESC 表名`，可批量查询）。

##### 常用查询

sql

```sql
-- 1. 查询指定表（mydb.users）的所有字段详情（等价于 DESC mydb.users，但信息更全）
SELECT 
  COLUMN_NAME AS 字段名,
  DATA_TYPE AS 数据类型, -- 简化类型（如 int、varchar）
  COLUMN_TYPE AS 完整数据类型, -- 完整类型（如 int(11)、varchar(50)）
  IS_NULLABLE AS 是否允许为空, -- YES/NO
  COLUMN_DEFAULT AS 默认值, -- 字段默认值
  COLUMN_COMMENT AS 字段注释, -- 字段的 COMMENT
  ORDINAL_POSITION AS 字段顺序 -- 字段在表中的位置（1开始）
FROM information_schema.COLUMNS 
WHERE TABLE_SCHEMA = 'mydb' -- 数据库名
  AND TABLE_NAME = 'users'; -- 表名

-- 2. 查询表中“非空且有默认值”的字段
SELECT COLUMN_NAME AS 字段名 
FROM information_schema.COLUMNS 
WHERE TABLE_SCHEMA = 'mydb' 
  AND TABLE_NAME = 'users' 
  AND IS_NULLABLE = 'NO' 
  AND COLUMN_DEFAULT IS NOT NULL;

-- 3. 批量查询数据库中所有表的“主键字段”（结合 KEY_COLUMN_USAGE）
SELECT 
  c.TABLE_NAME AS 表名,
  c.COLUMN_NAME AS 主键字段
FROM information_schema.COLUMNS c
JOIN information_schema.KEY_COLUMN_USAGE k
  ON c.TABLE_SCHEMA = k.TABLE_SCHEMA 
  AND c.TABLE_NAME = k.TABLE_NAME 
  AND c.COLUMN_NAME = k.COLUMN_NAME
WHERE c.TABLE_SCHEMA = 'mydb' 
  AND k.CONSTRAINT_NAME = 'PRIMARY'; -- 主键约束名固定为 PRIMARY
```

#### 4. INDEXES：查询表的索引信息

存储每个表的索引信息（索引名、索引类型、关联字段等），用于查看表的索引设计（替代 `SHOW INDEX FROM 表名`）。

##### 常用查询

sql

```sql
-- 1. 查询指定表（mydb.users）的所有索引
SELECT 
  INDEX_NAME AS 索引名, -- PRIMARY 表示主键索引
  INDEX_TYPE AS 索引类型, -- BTREE（默认）、HASH 等
  COLUMN_NAME AS 索引关联字段,
  NON_UNIQUE AS 是否非唯一 -- 0=唯一索引，1=普通索引
FROM information_schema.INDEXES 
WHERE TABLE_SCHEMA = 'mydb' 
  AND TABLE_NAME = 'users';

-- 2. 查询数据库中所有“唯一索引”的信息
SELECT 
  TABLE_NAME AS 表名,
  INDEX_NAME AS 索引名,
  COLUMN_NAME AS 关联字段
FROM information_schema.INDEXES 
WHERE TABLE_SCHEMA = 'mydb' 
  AND NON_UNIQUE = 0; -- 0=唯一索引

-- 3. 统计每个表的索引数量
SELECT 
  TABLE_NAME AS 表名,
  COUNT(*) AS 索引总数
FROM information_schema.INDEXES 
WHERE TABLE_SCHEMA = 'mydb' 
GROUP BY TABLE_NAME;
```

#### 5. KEY_COLUMN_USAGE：查询外键信息

存储表的约束信息（主键、外键、唯一约束等），重点用于查询外键关联关系（比如 “某表的外键关联了哪个表的哪个字段”）。

##### 常用查询

sql

```sql
-- 1. 查询指定数据库（mydb）的所有外键信息
SELECT 
  CONSTRAINT_NAME AS 外键约束名,
  TABLE_NAME AS 子表名, -- 包含外键的表
  COLUMN_NAME AS 子表外键字段,
  REFERENCED_TABLE_NAME AS 父表名, -- 被关联的表
  REFERENCED_COLUMN_NAME AS 父表关联字段 -- 被关联的字段
FROM information_schema.KEY_COLUMN_USAGE 
WHERE TABLE_SCHEMA = 'mydb' 
  AND REFERENCED_TABLE_NAME IS NOT NULL; -- 过滤非外键约束

-- 2. 查询某张表（如 orders）的外键关联关系
SELECT 
  COLUMN_NAME AS 外键字段,
  REFERENCED_TABLE_NAME AS 父表名,
  REFERENCED_COLUMN_NAME AS 父表字段
FROM information_schema.KEY_COLUMN_USAGE 
WHERE TABLE_SCHEMA = 'mydb' 
  AND TABLE_NAME = 'orders' 
  AND REFERENCED_TABLE_NAME IS NOT NULL;
```

### 三、高级组合查询示例

结合多个系统表，实现更复杂的需求：

#### 1. 生成数据库所有表的 “表名 - 字段 - 类型 - 注释” 完整清单

sql

```sql
SELECT 
  t.TABLE_NAME AS 表名,
  t.TABLE_COMMENT AS 表注释,
  c.COLUMN_NAME AS 字段名,
  c.COLUMN_TYPE AS 字段类型,
  c.COLUMN_COMMENT AS 字段注释
FROM information_schema.TABLES t
JOIN information_schema.COLUMNS c
  ON t.TABLE_SCHEMA = c.TABLE_SCHEMA 
  AND t.TABLE_NAME = c.TABLE_NAME
WHERE t.TABLE_SCHEMA = 'mydb' -- 替换为你的数据库名
ORDER BY t.TABLE_NAME, c.ORDINAL_POSITION;
```

#### 2. 检查数据库中 “没有主键” 的表（运维必备）

sql

```sql
SELECT TABLE_NAME AS 无主键表名 
FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'mydb' 
  AND TABLE_NAME NOT IN (
    SELECT DISTINCT TABLE_NAME 
    FROM information_schema.KEY_COLUMN_USAGE 
    WHERE TABLE_SCHEMA = 'mydb' 
      AND CONSTRAINT_NAME = 'PRIMARY'
  );
```

#### 3. 查询所有表的 “数据大小”（单位：MB）

sql

```sql
SELECT 
  TABLE_NAME AS 表名,
  ROUND(DATA_LENGTH / 1024 / 1024, 2) AS 数据大小MB, -- 数据文件大小
  ROUND(INDEX_LENGTH / 1024 / 1024, 2) AS 索引大小MB, -- 索引文件大小
  ROUND((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024, 2) AS 总大小MB
FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'mydb' 
ORDER BY 总大小MB DESC;
```

### 四、使用注意事项

1. **性能问题**：`information_schema` 的表是虚拟表，查询时会扫描 MySQL 元数据。如果数据库实例中有大量表（如上万张），查询可能较慢，建议在非高峰期执行。
2. **字段区分**：
    - `TABLE_SCHEMA`：始终表示 “数据库名”（不要写成 `DATABASE_NAME`）。
    - `COLUMN_TYPE` 是完整字段类型（如 `varchar(50) NOT NULL`），`DATA_TYPE` 是简化类型（如 `varchar`）。
3. **权限限制**：普通用户只能查询自己有权限的数据库 / 表的元数据，超级管理员（root）可查询所有。
4. **替代传统命令**：`information_schema` 可替代以下传统命令，且支持更灵活的过滤和统计：
    - `SHOW DATABASES` → 查询 `SCHEMATA`
    - `SHOW TABLES` → 查询 `TABLES`
    - `DESC 表名` → 查询 `COLUMNS`
    - `SHOW INDEX FROM 表名` → 查询 `INDEXES`

### 五、常用系统表速查表

| 系统表名               | 核心作用          | 关键字段                                                          |
| ------------------ | ------------- | ------------------------------------------------------------- |
| `SCHEMATA`         | 数据库信息         | `SCHEMA_NAME`（数据库名）、`DEFAULT_CHARACTER_SET_NAME`（字符集）         |
| `TABLES`           | 表基本信息         | `TABLE_NAME`（表名）、`ENGINE`（存储引擎）、`TABLE_COMMENT`（表注释）          |
| `COLUMNS`          | 字段详情          | `COLUMN_NAME`（字段名）、`COLUMN_TYPE`（字段类型）、`COLUMN_COMMENT`（字段注释） |
| `INDEXES`          | 索引信息          | `INDEX_NAME`（索引名）、`COLUMN_NAME`（关联字段）、`NON_UNIQUE`（是否唯一）      |
| `KEY_COLUMN_USAGE` | 约束（主键 / 外键）信息 | `REFERENCED_TABLE_NAME`（父表名）、`REFERENCED_COLUMN_NAME`（父表字段）   |
| `USER_PRIVILEGES`  | 用户权限信息        | `USER`（用户名）、`PRIVILEGE_TYPE`（权限类型）                            |
