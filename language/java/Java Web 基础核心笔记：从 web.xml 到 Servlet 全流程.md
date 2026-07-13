

---

# 📘 Java Web 基础核心笔记：从 `web.xml` 到 Servlet 全流程

> **适用场景**：纯 Servlet + JSP + XML 配置的传统 Java Web 项目（非 Spring Boot）。
> **核心目标**：理解“浏览器请求 -> 服务器处理 -> 页面响应”的底层运行机制。


## 第一章：核心配置文件 —— `web.xml`

### 1.1 它是谁？
- **全称**：Web 应用部署描述符（Deployment Descriptor）。
- **位置**：必须放在 `WEB-INF` 目录下。
- **本质**：Web 容器的**总指挥台**，采用 XML 格式。

### 1.2 核心职责（四大功能）
| 功能 | 作用说明 |
| :--- | :--- |
| **① 注册 Servlet** | 将 Java 类与具体的 URL 访问地址绑定。 |
| **② 注册 Filter** | 配置请求拦截器（如登录校验、防 XSS 攻击）。 |
| **③ 配置监听器** | 监听应用启动、Session 创建等生命周期事件。 |
| **④ 设置欢迎页** | 指定访问根路径（`/`）时默认展示的页面。 |


## 第二章：三大核心组件（Filter / Servlet / JSP）

### 2.1 安检员 —— Filter（过滤器）
- **执行时机**：**在 Servlet 之前**执行。
- **核心方法**：`doFilter(ServletRequest, ServletResponse, FilterChain)`
- **关键动作**：
  - **拦截**：不调用 `chain.doFilter()`，请求到此为止。
  - **放行**：调用 `chain.doFilter()`，请求继续向后传递。
- **生命周期**：`init()` -> `doFilter()`（多次） -> `destroy()`

### 2.2 快递员 —— Servlet（控制器）
- **执行时机**：**在 Filter 放行之后**执行。
- **核心方法**：`doGet()` / `doPost()`（由 `service()` 自动分发）。
- **核心任务**：
  1. 接收请求参数（`req.getParameter()`）。
  2. 调用业务逻辑（操作数据库、计算等）。
  3. 存储结果数据（`req.setAttribute()`）。
  4. 跳转页面（`req.getRequestDispatcher().forward()`）。
- **生命周期（单例）**：`init()`（仅一次） -> `service()`（多次） -> `destroy()`（仅一次）

### 2.3 打印员 —— JSP（视图）
- **本质**：在 HTML 中嵌入 Java 代码，用于动态渲染页面。
- **取值方式**：使用 EL 表达式（如 `${userName}`）提取 Servlet 存入的数据。
- **特点**：让前端展示与后端逻辑分离（早期 MVC 模式中的 V）。


## 第三章：全流程实战代码笔记（完整示例）

> **业务场景**：浏览器访问 `/hello?name=小明`，页面显示“你好，小明”；若不带 `name` 参数，被 Filter 拦截并报错。

### 📄 文件 1：MyFilter.java（安检逻辑）
```java
package com.example.filter;
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.IOException;

public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) resp;
        
        String name = request.getParameter("name");
        // 拦截条件：参数为空
        if (name == null || name.trim().isEmpty()) {
            response.setContentType("text/html;charset=utf-8");
            response.getWriter().write("<h1>拦截：请携带 ?name=xxx 参数</h1>");
            return; // 阻断请求
        }
        // 放行条件：参数存在
        chain.doFilter(request, response);
    }
}
```

### 📄 文件 2：MyServlet.java（业务分发）
```java
package com.example.servlet;
import javax.servlet.ServletException;
import javax.servlet.http.*;
import java.io.IOException;

public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        // 1. 获取参数（Filter已保证非空）
        String name = req.getParameter("name");
        // 2. 存入请求域
        req.setAttribute("userName", name);
        // 3. 转发至 JSP
        req.getRequestDispatcher("/result.jsp").forward(req, resp);
    }
}
```

### 📄 文件 3：result.jsp（视图展示）
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head><title>欢迎页</title></head>
<body>
    <h1 style="color:green;">你好，${userName}！欢迎来到 Java Web。</h1>
</body>
</html>
```

### 📄 文件 4：web.xml（组件注册总表）
```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" version="4.0">
    <!-- 注册 Servlet -->
    <servlet>
        <servlet-name>myServlet</servlet-name>
        <servlet-class>com.example.servlet.MyServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>myServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>

    <!-- 注册 Filter -->
    <filter>
        <filter-name>myFilter</filter-name>
        <filter-class>com.example.filter.MyFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>myFilter</filter-name>
        <url-pattern>/hello</url-pattern> <!-- 只拦截 /hello -->
    </filter-mapping>
</web-app>
```


## 第四章：运行结果推演（两种场景）

| 访问地址 | 执行流程 | 最终页面显示 |
| :--- | :--- | :--- |
| `http://.../hello`（无参数） | 进入 Filter -> 命中 `return` 拦截 -> **未执行** Servlet | 红字提示“拦截：请携带...” |
| `http://.../hello?name=张三`（有参数） | 进入 Filter -> 放行 -> 进入 Servlet -> 转发到 result.jsp | 绿字显示“你好，张三！...” |


## 第五章：新旧时代对照表（Servlet 基础 → Spring Boot）

> **为什么学了 Spring Boot 还要学这个？** 因为 Spring Boot 底层完全封装了 Servlet，只是换了一种更优雅的方式表达。

| 基础组件（本笔记） | Spring Boot 中的对应实现 |
| :--- | :--- |
| `web.xml` 手动配置 | `@WebServlet` / `@WebFilter` 注解，或 Java 配置类 |
| `HttpServlet` 继承类 | `@Controller` + `@RequestMapping` / `@GetMapping` |
| `req.setAttribute()` + 转发 | `ModelAndView` 或直接 return `Model` |
| `Filter` + `web.xml` 注册 | `@Component` + `@Order` 或 `FilterRegistrationBean` |
| 部署需外置 Tomcat | **内置** Tomcat / Jetty，直接跑 `main()` 方法 |


## 第六章：核心流程图解（逻辑闭环）

```text
[浏览器] 
   ↓ 发起请求
[web.xml 匹配 URL] → 找到对应的 Filter 和 Servlet
   ↓ 
[Filter 安检] 
   ├─ 失败 → 直接返回错误响应（拦截）
   └─ 成功 → chain.doFilter() 放行
        ↓
   [Servlet 业务处理] 
        ↓ 
   [JSP 渲染填充] 
        ↓ 
[浏览器] ← 返回最终 HTML 页面
```

---

**💡 笔记总结**：无论框架如何演进，Web 开发的本质始终是 **“接收请求 -> 处理逻辑 -> 返回响应”**。掌握 `web.xml` + Servlet + Filter + JSP 这套基础闭环，能帮你彻底吃透 MVC 架构的底层奥秘，未来看任何框架的报错和源码都将轻车熟路。