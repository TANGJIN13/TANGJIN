来了！既然你盯准了**“映射”**（Bind Mount），我就把其他乱七八糟的（Volume、tmpfs）全删掉，给你整一份**纯享版实战笔记**。

这份笔记的核心就一句话：**把电脑上的文件夹/文件，硬塞进容器里，两边实时同步。**

---

# Docker 绑定挂载（Bind Mount）实战笔记
## —— 就是那个“映射”的东西

### 1. 核心认知（一句话记住）
> **宿主机路径** 完全覆盖 **容器内路径**。
> 容器启动后，里面原本自带的文件（如果有）会被直接**隐藏**，显示的是你电脑上的内容。

### 2. 基础命令格式（必背模板）
```bash
docker run -v 宿主机绝对路径:容器内绝对路径 镜像名

# 或者用 --mount（更啰嗦，但脚本更安全）
docker run --mount type=bind,source=宿主机路径,target=容器路径 镜像名
```

### 3. 路径写法（新手 90% 的坑在这）

| 你要映射什么 | 正确写法 | 错误写法（会变成 Volume） |
| :--- | :--- | :--- |
| **当前目录**（最常用） | `-v $(pwd):/app` | `-v pwd:/app` ❌ |
| **当前目录下的子文件夹** | `-v ./code:/app` | `-v code:/app` ❌ |
| **绝对路径（Linux/Mac）** | `-v /home/user/data:/data` | `-v home/user/data:/data` ❌ |
| **Windows 盘符（Git Bash）** | `-v /c/Users/admin:/data` | `-v C:\Users:/data` ❌（格式不兼容）|

> 💡 **记死记硬背**：只要冒号左边 **带 `.`**（点）或者 **带 `/`**（根目录），就是映射！否则就是数据卷。

---

### 4. 文件 vs 目录的“变态”区别（必看）
- **映射目录（推荐）**：`-v ./data:/app/data`
- **映射单个文件（小心！）**：`-v ./config.yml:/app/config.yml`
  > ⚠️ **致命坑**：在 Docker Desktop（Mac/Win）上，如果宿主机没有这个文件，Docker 会**在容器里创建一个同名文件夹**而不是文件，导致容器启动报错。建议**先 touch 创建好文件**再挂载。

---

### 5. 只读模式（防止容器乱改你的电脑文件）
在末尾加 `:ro`（read only）：
```bash
docker run -v $(pwd):/app:ro nginx
# 容器内对 /app 的写入会直接报错 Permission denied
```

---

### 6. 实战场景命令清单

| 你想干嘛 | 一键复制命令 |
| :--- | :--- |
| **本地代码挂载进容器开发（热更新）** | `docker run -v $(pwd):/usr/src/app -w /usr/src/app node npm run dev` |
| **把 Nginx 的首页换成自己写的 HTML** | `docker run -v $(pwd)/html:/usr/share/nginx/html:ro -p 8080:80 nginx` |
| **用容器跑脚本，处理当前目录的数据** | `docker run --rm -v $(pwd):/data alpine ls /data`（`--rm` 用完即删）|
| **挂载宿主机的配置文件覆盖容器默认配置** | `docker run -v /etc/my.conf:/app/conf/app.conf:ro my_app` |

---

### 7. 查看当前容器到底“映射”了啥
```bash
docker inspect 容器名 | grep -A 10 "Mounts"
```
或者直接看更清晰的 JSON：
```bash
docker inspect 容器名 --format='{{json .Mounts}}' | jq .
```
你会看到 `"Type": "bind"` 和 `"Source"`（宿主机路径）、`"Destination"`（容器路径）。

---

### 8. 三大终极痛点避坑（亲身经历）

**痛点一：Permission Denied（没权限）**
- 现象：容器内进程（如 MySQL、Nginx）无法写入映射目录。
- 解决：要么 `chmod -R 777 ./data`（开发环境偷懒），要么跑容器时指定用户 ID：`docker run -u $(id -u):$(id -g) -v $(pwd):/app`。

**痛点二：Mac/Windows 性能巨慢（I/O 延迟）**
- 现象：挂载代码目录跑 `npm install` 或编译，慢到怀疑人生。
- 解决：`node_modules` 不要挂载！使用 **匿名卷** 把依赖放在容器内部：
  ```bash
  docker run -v $(pwd):/app -v /app/node_modules node npm install
  ```
  （第二个 `-v /app/node_modules` 没有左边路径，意为“保留容器内原本的 node_modules，不被映射覆盖”）

**痛点三：容器启动后目录是空的**
- 原因：你映射的宿主机目录是空的，它把容器里有内容的目录覆盖成了空文件夹。
- 解决：先把宿主机目录填上东西，或者换个非空的路径映射。

---

### 9. 终极记忆脑图（截图存手机）
```text
-v 冒号左边
    ├── 有 . 或 /  →  绑定映射（Bind）→ 修改电脑上的文件
    └── 纯字母命名  →  数据卷（Volume）→ 存在Docker地盘里（不算是映射）
```

> **生产环境警告**：Bind Mount 只在**开发调试**时用！上了生产（K8s/云服务器），请改用 **数据卷（Volume）** 或 **云存储**，因为绑定路径一旦变动，容器可能就起不来了。

搞定！把这份笔记存下来，以后再也不会把 `-v my_data:/data` 当成映射当前文件夹了。😎