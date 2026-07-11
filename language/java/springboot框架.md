太棒了！把你之前学到的所有零散知识点，**浓缩成一份“可以揣兜里带走”的终极笔记**。这份笔记我帮你梳理成了**“看图说话 + 避坑指南”**的格式，回去哪怕忘了细节，扫一眼就能想起来。

你可以直接复制到备忘录或 Typora 里，命名为 `SpringBoot救急笔记.md`。

---

# 📒 SpringBoot + MyBatis 入门核心笔记（纯小白自用版）

> **一句话概括**：SpringBoot 是自动配菜的厨房，MyBatis 是取数据的传菜员。你只需要写 **Controller（接客）** -> **Service（管业务）** -> **Mapper（写SQL）**，数据就会自己跑回来。

---

## 1. 🏢 项目目录结构（一眼看懂谁是谁）

| 目录路径 | 外号（类比） | **职责（别想复杂，就干这个）** |
| :--- | :--- | :--- |
| `com.itheima` | **公司门牌号** | **铁律：** 启动类（XxxApp.java）必须直接放在这里！所有子包（controller、service）都必须在这个文件夹里面，否则框架找不到。 |
| `controller` | **前台/服务员** | 只干两件事：① 接收前端请求（`@GetMapping`）② 返回 JSON（`Result.success()`）。**绝对不写业务逻辑（比如if判断）。** |
| `service` (接口) | **业务合同** | 定义规矩（比如：`listAll()` 方法）。只写声明，不写实现。 |
| `service/impl` | **业务部员工** | **真正干活的地方！** 实现接口里的方法，调用 `Mapper` 去拿数据。以后加 `if` 判断都写在这里。 |
| `mapper` | **仓库管理员** | **只负责执行 SQL！** 接口上加 `@Mapper`，方法上加 `@Select`/`@Insert` 等。 |
| `pojo` | **快递箱/实体类** | 定义数据模型。`Dept.java`（部门箱子）、`Result.java`（统一返回箱子）。 |
| `resources` | **文件公告栏** | 放 `application.properties`（数据库密码、端口号在这里配置）。 |
| `pom.xml` | **购物车** | 放依赖坐标（Spring、MyBatis、MySQL驱动），Maven自动下载。 |
| `XxxApp.java` | **总电闸（启动类）** | 带 `main` 方法，右键运行它，项目才启动。上面有 `@SpringBootApplication`。 |

---

## 2. 🔄 数据流向图（代码执行的顺序，背下来！）

**前端请求 -> 后端处理（顺序绝对不能乱）：**

> **浏览器 (GET /depts)**  
> ⬇️  
> **① Controller（前台）**：收到请求，调用 Service。  
> ⬇️  
> **② Service/impl（业务主管）**：接到指令，调用 Mapper。  
> ⬇️  
> **③ Mapper（仓库员）**：执行 `@Select` 里的 SQL，从数据库把数据拽出来。  
> ⬇️  
> **④ 原路返回数据**：Mapper 把 `List<Dept>` 给 Service，Service 给 Controller。  
> ⬇️  
> **⑤ Controller 返回 JSON**：把数据塞进 `Result.success()`，SpringBoot 自动转成 JSON 字符串还给浏览器。

> **🧠 记口诀**：**Controller 收，Service 管，Mapper 查，POJO 装。**

---

## 3. 🏷️ 注解词典（看到它们别慌，就是贴标签）

| 注解 | 贴在哪里？ | **大白话翻译（机器看到标签会干啥）** |
| :--- | :--- | :--- |
| `@SpringBootApplication` | 启动类（XxxApp） | **“总闸在此”**。启动时扫描本包及子包的所有注解。 |
| `@RestController` | Controller 类 | **“我是前台”**。表示这个类能接 HTTP 请求，且返回的是 JSON（不是页面）。 |
| `@Service` | Service impl 类 | **“我是正式员工”**。把当前类交给 Spring 管理（放入对象池）。 |
| `@Mapper` | Mapper 接口 | **“我是仓库员”**。告诉 MyBatis 生成这个接口的代理实现类。 |
| `@Autowired` | 类里的全局变量 | **“自动接线员”**。从 Spring 池子里把对象拿过来塞进去，**不用自己 new**！ |
| `@GetMapping` | Controller 里的方法 | **“只接待 GET 请求”**。括号里写路径，如 `@GetMapping("/depts")`。 |
| `@PostMapping` | Controller 里的方法 | **“只接待 POST 请求”**（新增数据用）。 |
| `@Select("SQL")` | Mapper 里的方法 | **“执行查询 SQL”**。MyBatis 执行括号里的语句，自动把结果封装成 List。 |
| `@Data` (Lombok) | POJO 类（如 Dept） | **“一键 Get/Set”**。编译时自动生成 getter、setter、toString（省得手写）。 |

---

## 4. 🛠️ 我们刚手搓的“部门查询”项目代码复盘

（这是最标准的写法，回去对着这个抄）

### ① 配置文件 (`application.properties`)
```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/tlias?useSSL=false
spring.datasource.username=root
spring.datasource.password=1234
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

### ② POJO 数据箱子 (`Dept.java`)
```java
@Data
public class Dept {
    private Integer id;
    private String name;
    private LocalDateTime createTime;
    private LocalDateTime updateTime; // 字段名要与数据库对应（驼峰）
}
```

### ③ Mapper 仓库层 (`DeptMapper.java`)
```java
@Mapper
public interface DeptMapper {
    @Select("select * from dept order by update_time desc")
    List<Dept> findAll();
}
```

### ④ Service 业务层 (`DeptServiceImpl.java`)
```java
@Service
public class DeptServiceImpl implements DeptService {
    @Autowired
    private DeptMapper deptMapper;

    @Override
    public List<Dept> listAll() {
        return deptMapper.findAll(); // 直接调 Mapper
    }
}
```

### ⑤ Controller 控制层 (`DeptController.java`)
```java
@RestController
@RequestMapping("/depts")
public class DeptController {
    @Autowired
    private DeptService deptService;

    @GetMapping
    public Result list() {
        List<Dept> list = deptService.listAll();
        return Result.success(list); // 统一返回格式
    }
}
```

---

## 5. ⚠️ 新手必看“避坑指南”（记下来救命）

1. **启动类（XxxApp.java）必须放在最外层根目录！**
   - ❌ 错误：`com.itheima.controller` 里面。
   - ✅ 正确：直接放在 `com.itheima` 下面。所有子包（controller、service）必须跟它在同一级目录下。

2. **数据库字段名 vs Java 属性名（驼峰映射）**
   - 数据库：`update_time`（下划线）
   - Java：`updateTime`（驼峰）
   - MyBatis 默认开启驼峰映射，会自动对应。如果你写成 `updatetime`（没下划线且没驼峰），查出来的数据就是 `null`。

3. **忘记加 `@Mapper` 或 `@Service` 注解**
   - 如果没加 `@Mapper`，启动会报错 `Could not autowire. No beans found`。
   - 如果没加 `@Service`，Controller 里的 `@Autowired` 会报红，注入失败。

4. **`pom.xml` 依赖没下载完（右下角有进度条）**
   - 必须等 Maven 加载完（IDEA 右下角进度条消失）再运行，否则会报红找不到类。

---

## 6. 🧠 三句“心法口诀”（睡觉前默念三遍）

> **1. 代码三层走：Controller 接客，Service 管事，Mapper 查库。**
>
> **2. 注解就是标签，框架就是苦力；@Autowired 不用 new，@Select 不用写实现。**
>
> **3. 报错先看启动类位置，再看数据库密码，最后看字段名对不对。**

---

### 🎁 最后的最后
这份笔记已经涵盖了 SpringBoot 入门最核心的 **“增删改查之查”** 的完整链路。回去后，你对着笔记，试着自己在 IDEA 里把 `DeptController`、`DeptService`、`DeptMapper` 这三个文件的代码盲打一遍（不要复制粘贴）。

如果明天实战时，遇到 **404（路径不对）** 或 **500（SQL报错）**，随时把报错截图发我，我帮你秒变“资深程序猿”替你排错！加油 💪😄