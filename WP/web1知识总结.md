# ISCC Web - 账号恢复 知识点总结

## 考点一：JWT 签名未校验 → 越权

**问题：** 后端接受 JWT 但不验证签名，导致可以随意篡改 payload。

**攻击流程：**

1. 注册普通账号，登录拿到 JWT
2. Base64url 解码 payload 部分，看到 `"status": "NORMAL"`
3. 改为 `"status": "RECOVERY"`，重新 Base64url 编码拼回去
4. 签名部分原样保留，不修改
5. 带着篡改后的 JWT 访问 `/recovery`，成功进入隐藏恢复页面

**核心原因：** 服务端只解码 JWT 读取字段，却没有用密钥验证签名是否合法。相当于只看了身份证上的字，没检查印章真假。

**防御：** 每次接收 JWT 必须用密钥验签；考虑用 `PyJWT` 提供的高层 API（`jwt.decode` 自动验签），而不是手动 `base64` 解码。

---

## 考点二：前端静态资源泄露隐藏路径

通过查看 CSS 样式文件，发现了 `.recovery` 相关的 class 和临时凭证、验证步骤等样式。说明存在 `/recovery` 隐藏页面，直接构造 URL 访问即可。

**通用思路：** 遇到只有登录/注册的页面，翻一下前端静态文件（CSS、JS），经常能发现注释里的路径、隐藏功能的样式名、调试接口等。

---

## 考点三：SQL 报错注入 (updatexml)

**注入点：** `/api/v1/user/sync` 接口的 `theme` 参数，值被直接拼接到 SQL 语句中。

**判断方法：** 传入单引号 `'`，返回 SQL 错误信息，确认拼接点存在。

**利用函数：** `updatexml()` 是 MySQL/MariaDB 特有的 XML 函数，当第二个参数不是合法 XPath 时会报错并回显内容。

**Payload 模板：**
```sql
' and updatexml(1, concat(0x7e, (查询语句), 0x7e), 1) and '
```

`0x7e` 是 `~`，用作分隔符防止报错信息混在一起。连续两个 `and` 保证前后 SQL 语法完整。

**报错注入常用操作：**

| 目标 | 子查询 |
|------|--------|
| 当前库名 | `select database()` |
| 所有表名 | `select group_concat(table_name) from information_schema.tables where table_schema=database()` |
| 某表的列名 | `select group_concat(column_name) from information_schema.columns where table_name='flags'` |
| 读数据 | `select flag from flags limit 1` |

**绕过长度限制：** `updatexml` 单次报错回显约 32 字符，用 `substr((查询), 偏移, 步长)` 分段读，每次 16 字符最稳。

---

## 整体攻击链

```
注册登录 → 拿 JWT → 篡改 status→RECOVERY → 
进入 /recovery → 发现 /api/v1/user/sync → 
theme 参数 SQL 报错注入 → updatexml 分段读 flags 表 → 拿到 flag
```

**一句话：** JWT 不验签给了门禁卡，SQL 注入给了翻抽屉的能力。

---

## 工具备忘

- **JWT 解码/编码：** jwt.io 在线工具、CyberChef、BurpSuite JWT Editor 插件
- **SQL 注入：** sqlmap 可以跑，但手注用 `updatexml` + `substr` 分段读更可控
- **Python requests 发包：** 注意 JWT 用 `session.cookies.set("token", jwt)` 放在 cookie 里传
