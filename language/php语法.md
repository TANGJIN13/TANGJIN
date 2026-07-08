PHP 是一种广泛用于 Web 开发的**服务器端脚本语言**，主要用于构建动态网页、API 接口、后端服务等，具有跨平台、语法简洁、与 HTML 无缝集成等特点。以下是 PHP 核心知识点、入门示例和常见应用场景，帮助你快速上手或巩固基础：
#### . 基本语法

- PHP 代码需包裹在 `<?php ... ?>` 标签中（可嵌入 HTML 中）。
- 语句以分号 `;` 结束，注释支持 `// 单行注释`、`/* 多行注释 */`、`# 脚本注释`。
- 变量以 `$` 开头，弱类型（无需声明变量类型，自动适配）
- ### 一、PHP 基础入门

#### 1. 基本语法

- PHP 代码需包裹在 `<?php ... ?>` 标签中（可嵌入 HTML 中）。
- 语句以分号 `;` 结束，注释支持 `// 单行注释`、`/* 多行注释 */`、`# 脚本注释`。
- 变量以 `$` 开头，弱类型（无需声明变量类型，自动适配）。

php

```php
<?php
// 变量定义
$name = "PHP 编程";
$age = 25;
$isValid = true;

// 输出（echo 无返回值，print 有返回值 1）
echo "Hello, " . $name; // 字符串拼接用 .
echo "<br>年龄：" . $age;

// 注释示例
/*
这是多行注释
支持换行
*/
# 单行脚本注释
?>

<!-- PHP 可嵌入 HTML 任意位置 -->
<h1><?php echo $name; ?> 入门</h1>
```

#### 2. 数据类型

PHP 支持 8 种原始数据类型，无需显式声明：

- 标量类型：`string`（字符串）、`int`（整数）、`float`（浮点数）、`bool`（布尔值）
- 复合类型：`array`（数组）、`object`（对象）
- 特殊类型：`null`（空值）、`resource`（资源，如数据库连接）

php

```php
<?php
// 字符串（单引号不解析变量，双引号解析变量）
$str1 = 'Hello $name'; // 输出：Hello $name
$str2 = "Hello $name"; // 输出：Hello PHP 编程

// 数组（索引数组、关联数组）
$arr1 = [1, 2, 3]; // 索引数组（键为 0,1,2）
$arr2 = ["name" => "张三", "age" => 20]; // 关联数组（键为自定义字符串）
echo $arr2["name"]; // 输出：张三

// null（未赋值、赋值为 null、unset() 后的变量）
$var = null;
unset($age); // $age 变为 null
?>
```

#### 3. 流程控制

- 条件判断：`if-elseif-else`、`switch-case`
- 循环：`for`、`foreach`（遍历数组常用）、`while`、`do-while`

php

```php
<?php
// if-else
$score = 85;
if ($score >= 90) {
    echo "优秀";
} elseif ($score >= 60) {
    echo "及格";
} else {
    echo "不及格";
}

// foreach 遍历数组
$users = [
    ["name" => "李四", "age" => 22],
    ["name" => "王五", "age" => 23]
];
foreach ($users as $user) {
    echo "姓名：" . $user["name"] . "，年龄：" . $user["age"] . "<br>";
}

// switch
$level = "B";
switch ($level) {
    case "A":
        echo "优秀";
        break;
    case "B":
        echo "良好";
        break;
    default:
        echo "普通";
}
?>
```

#### 4. 函数

自定义函数使用 `function` 关键字，支持参数默认值、返回值，无函数重载（后定义的函数会覆盖前一个同名函数）。

php

```php
<?php
// 无参数无返回值
function sayHello() {
    echo "Hello PHP!";
}
sayHello();

// 带参数有返回值
function calculateSum($a, $b = 0) { // $b 默认值 0
    return $a + $b;
}
echo calculateSum(5, 3); // 输出 8
echo calculateSum(5); // 输出 5（$b 用默认值）
?>
```

### 二、PHP 核心应用场景

#### 1. 动态网页开发

PHP 可直接嵌入 HTML，根据用户请求动态生成页面内容（如用户登录状态、数据库数据渲染）。

php

```php
<!DOCTYPE html>
<html>
<head>
    <title>动态页面</title>
</head>
<body>
    <h1>欢迎访问 <?php echo $_SERVER["HTTP_HOST"]; ?></h1> <!-- 获取当前域名 -->
    <p>当前时间：<?php echo date("Y-m-d H:i:s"); ?></p> <!-- 生成当前时间 -->
    
    <?php if (isset($_GET["name"])): ?> <!-- 接收 URL 参数 ?name=xxx -->
        <p>你好，<?php echo $_GET["name"]; ?>！</p>
    <?php else: ?>
        <p>请通过 URL 传递 name 参数（例如：?name=张三）</p>
    <?php endif; ?>
</body>
</html>
```

#### 2. 表单处理（前后端交互）

PHP 接收前端表单提交的数据（`$_POST`/`$_GET` 超全局变量），进行验证、存储或逻辑处理。

html

```html
<!-- 前端表单（form.html） -->
<form method="post" action="handle_form.php">
    用户名：<input type="text" name="username" required><br>
    密码：<input type="password" name="password" required><br>
    <button type="submit">提交</button>
</form>
```

php

```php
<?php
// 后端处理（handle_form.php）
if ($_SERVER["REQUEST_METHOD"] === "POST") {
    // 接收表单数据
    $username = $_POST["username"];
    $password = $_POST["password"]; // 实际开发需加密存储（如 password_hash()）
    
    // 简单验证
    if ($username === "admin" && $password === "123456") {
        echo "登录成功！欢迎 " . $username;
    } else {
        echo "用户名或密码错误！";
    }
}
?>
```

#### 3. 数据库操作（MySQL 为例）

PHP 可通过 `PDO` 或 `mysqli` 扩展连接数据库，实现数据的增删改查（推荐使用 PDO，支持多数据库、防 SQL 注入）。

php

```php
<?php
// 1. 连接数据库（PDO）
$dsn = "mysql:host=localhost;dbname=test;charset=utf8mb4";
$user = "root";
$pass = "root";

try {
    $pdo = new PDO($dsn, $user, $pass);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    // 2. 查询数据（防 SQL 注入：绑定参数）
    $sql = "SELECT * FROM users WHERE username = :username";
    $stmt = $pdo->prepare($sql);
    $stmt->execute(["username" => "张三"]);
    $user = $stmt->fetch(PDO::FETCH_ASSOC); // 获取单条数据
    echo "查询到用户：" . $user["username"];

    // 3. 插入数据
    $insertSql = "INSERT INTO users (username, age) VALUES (:username, :age)";
    $stmt = $pdo->prepare($insertSql);
    $stmt->execute(["username" => "赵六", "age" => 24]);
    echo "插入成功，新用户 ID：" . $pdo->lastInsertId();

} catch(PDOException $e) {
    die("数据库错误：" . $e->getMessage());
}
?>
```

#### 4. API 接口开发

PHP 可返回 JSON 格式数据，作为前端（Vue/React/ 小程序）或其他服务的后端接口。

php

```php
<?php
// 设置响应头为 JSON 格式
header("Content-Type: application/json; charset=utf-8");

// 模拟数据（实际从数据库查询）
$users = [
    ["id" => 1, "name" => "张三", "age" => 20],
    ["id" => 2, "name" => "李四", "age" => 22]
];

// 响应 JSON 数据
echo json_encode([
    "code" => 200,
    "msg" => "success",
    "data" => $users
]);
?>
```

### 三、PHP 环境搭建

要运行 PHP 代码，需搭建服务器环境（推荐集成环境，简单高效）：

- **Windows**：XAMPP、WAMP（包含 Apache 服务器、MySQL 数据库、PHP 解释器）
- **Mac**：MAMP、XAMPP for Mac
- **Linux**：手动安装 Apache/Nginx + PHP + MySQL，或使用 LNMP 一键安装包

搭建后，将 PHP 文件放入服务器根目录（如 XAMPP 的 `htdocs` 文件夹），通过浏览器访问 `http://localhost/文件名.php` 即可运行。




