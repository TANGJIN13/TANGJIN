![[Pasted image 20251122213637.png]]
information_schema.column的模拟表
## 对应的 information_schema.columns 模拟表

|TABLE_SCHEMA|TABLE_NAME|COLUMN_NAME|DATA_TYPE|ORDINAL_POSITION|
|---|---|---|---|---|
|test_db|users|id|int|1|
|test_db|users|username|varchar|2|
|test_db|users|password|varchar|3|
|test_db|users|email|varchar|4|
|test_db|users|created_at|datetime|5|
|test_db|posts|id|int|1|
|test_db|posts|title|varchar|2|
|test_db|posts|content|text|3|
|test_db|posts|user_id|int|4|
|test_db|posts|created_at|datetime|5|
|test_db|comments|id|int|1|
|test_db|comments|post_id|int|2|
|test_db|comments|user_id|int|3|
|test_db|comments|comment|text|4|
|test_db|comments|created_at|datetime|5|