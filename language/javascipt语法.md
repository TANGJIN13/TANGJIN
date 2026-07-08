
JavaScript（简称 JS）是一门**弱类型、解释型**的脚本语言，核心用于网页交互（前端），也可通过 Node.js 用于后端开发。以下是 JS 核心语法体系，从基础到常用场景，帮你系统掌握：

### 一、基础语法

#### 1. 代码嵌入方式

JS 可嵌入 HTML 中，或单独作为 `.js` 文件引入：

html

```html
<!-- 1. 内嵌在 <script> 标签中（可放 <head> 或 <body> 末尾） -->
<script>
  console.log("Hello JavaScript!"); // 控制台输出（调试常用）
</script>

<!-- 2. 引入外部 JS 文件（推荐，分离代码） -->
<script src="index.js"></script>

<!-- 3. 行内脚本（不推荐，耦合度高） -->
<button onclick="alert('点击成功')">点击我</button>
```

#### 2. 变量与常量

- 变量：用 `let`（块级作用域，推荐）、`var`（函数级作用域，旧语法）声明，弱类型（无需指定类型）。
- 常量：用 `const` 声明，值不可修改（引用类型的内容可修改），必须初始化。
- 变量名规则：字母、数字、`_`、`$` 组成，不能以数字开头，区分大小写。

javascript

```javascript
// 变量
let name = "JS 语法"; // 字符串
let age = 28; // 数字
let isActive = true; // 布尔值
let user; // 未赋值，默认 undefined

// 常量（值不可改）
const PI = 3.14159;
// PI = 3.14; // 报错：常量不能重新赋值

// 引用类型常量（内容可改）
const arr = [1, 2, 3];
arr.push(4); // 合法，arr 变为 [1,2,3,4]
```

#### 3. 数据类型

JS 有 7 种**原始类型**和 1 种**引用类型**，原始类型按值传递，引用类型按引用传递：

|类型|说明|示例|
|---|---|---|
|`string`|字符串（单引号 / 双引号 / 反引号）|`'abc'`、`"123"`、${name}``|
|`number`|数字（整数 / 浮点数 / NaN/Infinity）|`10`、`3.14`、`NaN`（非数字）|
|`boolean`|布尔值（true/false）|`true`、`false`|
|`undefined`|未赋值的变量默认值|`let a;`（a 为 undefined）|
|`null`|主动表示 “空”（通常用于释放引用）|`let b = null;`|
|`symbol`|唯一标识（ES6 新增）|`const s = Symbol('id');`|
|`bigint`|大整数（解决 number 精度问题，ES2020）|`const num = 9007199254740991n;`|
|引用类型|对象（Object）、数组（Array）、函数等|`{name: '张三'}`、`[1,2,3]`|

javascript

```javascript
// 字符串（反引号支持换行和变量插值）
let username = "李四";
let info = `姓名：${username}，年龄：${age}`; // 插值语法

// 特殊数字
console.log(10 / 0); // Infinity（正无穷）
console.log("abc" * 2); // NaN（非数字，注意：NaN 不等于自身）

// 类型判断（typeof 运算符）
console.log(typeof "abc"); // string
console.log(typeof 123); // number
console.log(typeof null); // object（历史bug，需单独判断）
console.log(Array.isArray([1,2,3])); // true（数组专用判断）
```

#### 4. 运算符

- 算术运算符：`+`、`-`、`*`、`/`、`%`（取余）、`++`（自增）、`--`（自减）
- 赋值运算符：`=`、`+=`、`-=`、`*=`、`/=`
- 比较运算符：`==`（松散相等，自动类型转换）、`===`（严格相等，不转换类型，推荐）、`!=`、`!==`、`>`、`<`、`>=`、`<=`
- 逻辑运算符：`&&`（与）、`||`（或）、`!`（非）
- 字符串运算符：`+`（拼接）

javascript

```javascript
// 比较运算符（重点：== vs ===）
console.log(1 == "1"); // true（自动转换类型）
console.log(1 === "1"); // false（类型不同，严格判断）

// 逻辑运算符（短路求值）
let a = 0 || "默认值"; // "默认值"（0 为假，取后者）
let b = 1 && "有效"; // "有效"（1 为真，取后者）

// 自增/自减（前置先运算后赋值，后置先赋值后运算）
let x = 5;
console.log(x++); // 5（先输出 x，再 x+1）
console.log(++x); // 7（先 x+1，再输出）
```

### 二、流程控制

#### 1. 条件判断

- `if-else if-else`：基础条件判断
- `switch-case`：多值匹配（注意 `break` 避免穿透）

javascript

```javascript
let score = 75;

// if-else if-else
if (score >= 90) {
  console.log("优秀");
} else if (score >= 60) {
  console.log("及格");
} else {
  console.log("不及格");
}

// switch-case（推荐用 === 匹配，需加 break）
let fruit = "apple";
switch (fruit) {
  case "apple":
    console.log("苹果");
    break; // 必须加，否则继续执行下一个 case
  case "banana":
    console.log("香蕉");
    break;
  default:
    console.log("未知水果");
}
```

#### 2. 循环语句

- `for`：固定次数循环（常用）
- `for...of`：遍历数组 / 字符串（ES6+，推荐）
- `for...in`：遍历对象属性（不推荐遍历数组，会遍历原型链）
- `while`：条件满足时循环
- `do...while`：先执行一次，再判断条件

javascript

```javascript
// 1. for 循环（遍历数组）
let arr = [1, 2, 3, 4];
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]); // 1,2,3,4
}

// 2. for...of 循环（简洁遍历数组/字符串）
for (let item of arr) {
  console.log(item); // 1,2,3,4
}

// 3. while 循环
let count = 0;
while (count < 3) {
  console.log(count); // 0,1,2
  count++;
}

// 4. do...while（至少执行一次）
let num = 5;
do {
  console.log(num); // 5
  num--;
} while (num < 5);
```

### 三、函数

函数是 JS 的核心，用于封装可复用逻辑，有 3 种常用定义方式：

#### 1. 函数声明（Function Declaration）

javascript

```javascript
// 定义函数
function add(a, b = 0) { // b 为默认参数（ES6+）
  return a + b; // 返回值
}

// 调用函数
console.log(add(2, 3)); // 5
console.log(add(5)); // 5（b 用默认值 0）
```

#### 2. 函数表达式（Function Expression）

javascript

```javascript
// 匿名函数表达式
const multiply = function(a, b) {
  return a * b;
};

// 箭头函数（ES6+，简洁，无 this 绑定）
const subtract = (a, b) => a - b; // 单条返回语句可省略 {} 和 return
const greet = (name) => `Hello, ${name}!`;

console.log(multiply(3, 4)); // 12
console.log(subtract(10, 4)); // 6
console.log(greet("张三")); // Hello, 张三!
```

**箭头函数注意事项**：

- 无自己的 `this`（继承外层作用域的 `this`），不能用作构造函数（不能 `new`）。
- 无 `arguments` 对象（需用剩余参数 `...` 替代）。

#### 3. 剩余参数与展开运算符（ES6+）

javascript

```javascript
// 剩余参数（接收多个参数为数组）
function sum(...nums) {
  return nums.reduce((total, num) => total + num, 0);
}
console.log(sum(1, 2, 3, 4)); // 10

// 展开运算符（拆分数组/对象）
let arr1 = [1, 2, 3];
let arr2 = [...arr1, 4, 5]; // [1,2,3,4,5]

let obj1 = { name: "张三" };
let obj2 = { ...obj1, age: 20 }; // { name: "张三", age: 20 }
```

### 四、数组（Array）

数组是 JS 中最常用的引用类型，用于存储有序集合，内置大量实用方法：

javascript

```javascript
// 数组创建
let fruits = ["apple", "banana", "orange"];
let emptyArr = new Array(3); // [empty × 3]（长度为 3 的空数组）

// 数组访问与修改
console.log(fruits[0]); // apple（通过索引访问）
fruits[1] = "grape"; // 修改元素，fruits 变为 ["apple", "grape", "orange"]

// 常用数组方法
fruits.push("mango"); // 尾部添加元素 → ["apple", "grape", "orange", "mango"]
fruits.pop(); // 尾部删除元素 → ["apple", "grape", "orange"]
fruits.unshift("pear"); // 头部添加元素 → ["pear", "apple", "grape", "orange"]
fruits.shift(); // 头部删除元素 → ["apple", "grape", "orange"]

// 遍历与转换
fruits.forEach((fruit, index) => {
  console.log(`索引 ${index}：${fruit}`); // 遍历每个元素
});

let upperFruits = fruits.map(fruit => fruit.toUpperCase()); // 映射为大写 → ["APPLE", "GRAPE", "ORANGE"]
let longFruits = fruits.filter(fruit => fruit.length > 5); // 过滤长度>5的元素 → ["orange"]
let totalLength = fruits.reduce((sum, fruit) => sum + fruit.length, 0); // 求和 → 5+5+6=16

// 其他常用方法
console.log(fruits.includes("apple")); // true（是否包含元素）
console.log(fruits.indexOf("grape")); // 1（元素索引，不存在返回 -1）
console.log(fruits.join("-")); // "apple-grape-orange"（数组转字符串）
```

### 五、对象（Object）

对象是 JS 的核心概念，用于存储键值对（属性和方法），是引用类型：

javascript

```javascript
// 对象创建
let user = {
  name: "李四", // 属性（键：name，值："李四"）
  age: 22,
  isStudent: true,
  sayHi: function() { // 方法（属性值为函数）
    console.log(`你好，我是 ${this.name}`); // this 指向当前对象
  },
  address: { // 嵌套对象
    city: "北京",
    district: "朝阳区"
  }
};

// 访问属性
console.log(user.name); // 李四（点语法，推荐）
console.log(user["age"]); // 22（方括号语法，适用于键含特殊字符或变量）

// 修改属性
user.age = 23; // 修改值
user.gender = "男"; // 添加新属性
delete user.isStudent; // 删除属性

// 调用方法
user.sayHi(); // 你好，我是 李四

// 遍历对象属性（for...in）
for (let key in user) {
  console.log(`${key}：${user[key]}`);
}

// ES6+ 简化语法（对象字面量增强）
let name = "王五";
let age = 25;
let newUser = {
  name, // 键值同名可简写
  age,
  sayHello() { // 方法简写（省略 function）
    console.log(`Hello, ${this.name}`);
  }
};
```

### 六、特殊语法与注意事项

#### 1. `this` 关键字

`this` 指向**当前执行上下文**，随调用方式变化：

- 普通函数调用：`this` 指向全局对象（浏览器：`window`，Node.js：`global`），严格模式下为 `undefined`。
- 对象方法调用：`this` 指向调用方法的对象。
- 构造函数调用（`new`）：`this` 指向新创建的对象。
- 箭头函数：无自己的 `this`，继承外层作用域的 `this`。

javascript

```javascript
function testThis() {
  console.log(this);
}

testThis(); // 浏览器中输出 window（非严格模式）

let obj = {
  fn: testThis
};
obj.fn(); // 输出 obj（this 指向 obj）

// 箭头函数无 this 绑定
let arrowFn = () => {
  console.log(this);
};
arrowFn(); // 输出外层 this（如 window）
```

#### 2. 解构赋值（ES6+）

快速从数组 / 对象中提取值，简化代码：

javascript

```javascript
// 数组解构
let [a, b, c] = [1, 2, 3];
console.log(a); // 1，b：2，c：3

// 对象解构
let { name, age } = user; // 从 user 对象提取 name 和 age
console.log(name); // 李四，age：23

// 解构默认值
let [x, y = 5] = [3]; // x：3，y：5（未提取到则用默认值）
```

#### 3. 模板字符串（ES6+）

用反引号 `` ` `` 包裹，支持换行和变量插值：

javascript

```javascript
let title = "JS 语法";
let content = `这是一篇关于 ${title} 的教程
支持换行
无需拼接字符串`;
console.log(content);
```

#### 4. 严格模式（Strict Mode）

在脚本或函数开头添加 `"use strict";`，开启严格模式，避免 JS 语法缺陷：

- 禁止未声明变量赋值。
- 禁止删除变量 / 函数。
- `this` 不再指向全局对象（普通函数调用时为 `undefined`）。
- 禁止重复参数名。

javascript

```javascript
"use strict";
x = 10; // 报错：x 未声明
```

### 七、常用场景示例

#### 1. DOM 操作（前端核心）

JS 可操作 HTML 元素（DOM），实现交互效果：

html

```html
<button id="btn">点击修改文本</button>
<p id="text">原始文本</p>

<script>
  // 获取元素
  let btn = document.getElementById("btn");
  let text = document.getElementById("text");

  // 绑定点击事件
  btn.addEventListener("click", function() {
    text.textContent = "修改后的文本"; // 修改文本内容
    text.style.color = "red"; // 修改样式
  });
</script>
```

#### 2. 异步编程（回调函数）

JS 是单线程，异步操作（如网络请求、定时器）用回调函数处理：

javascript

```javascript
// 定时器（异步）
setTimeout(function() {
  console.log("1 秒后执行");
}, 1000);

// 网络请求（fetch API，异步）
fetch("https://api.example.com/data")
  .then(response => response.json())
  .then(data => console.log("请求成功：", data))
  .catch(error => console.log("请求失败：", error));
```