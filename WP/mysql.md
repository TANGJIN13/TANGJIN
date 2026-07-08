```php
mysql_query
````mysql_query` 是 **PHP 早期用于执行 MySQL 数据库 SQL 语句的函数**，但因安全缺陷和版本兼容问题，已被 PHP 官方**彻底废弃**（PHP 5.5 后弃用，PHP 7 完全移除）。以下是它的核心解析：

### 一、基本用法

语法：`mysql_query($sql, $conn)`

- `$sql`：要执行的 SQL 语句（如 `SELECT * FROM users`）；
- `$conn`：可选的数据库连接资源（由 `mysql_connect` 创建，默认使用最近的连接）；
- **返回值**：
    - 若为**查询语句**（`SELECT`、`SHOW` 等），返回**结果集资源**（需配合 `mysql_fetch_array` 等函数提取数据）；
    - 若为**写操作**（`INSERT`、`UPDATE`、`DELETE` 等），返回`true`（成功）或`false`（失败）