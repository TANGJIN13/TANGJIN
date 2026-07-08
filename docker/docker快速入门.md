没问题！这份笔记把你今天从零到一搞懂的 **Docker 核心概念 + MySQL 实战细节** 全部串起来了。直接复制保存到你的 Markdown 笔记软件（如 Typora、Notion、Obsidian）里即可。

---

# 🐳 Docker 核心概念与 MySQL 实战笔记

> **笔记时间：** 2026-07-07
> **核心目标：** 彻底搞懂 Docker 是什么、端口映射、数据持久化，以及 MySQL 容器的启动原理。

## 1. Docker 到底是什么？（纠正误区）
- **Docker 不是虚拟机**，它是一个**容器化平台**。
- **本质：** 是一个 **C/S 架构的软件**（客户端 + 守护进程）。
- **误区纠正**：`docker run mysql` 后，MySQL 是跑在**你自己的电脑/服务器**上，而不是跑在 Docker 公司的服务器上。
- **三要素**：镜像（Image 安装包）、容器（Container 运行中的进程）、仓库（Registry 存放镜像的服务器，如 Docker Hub）。

## 2. 端口映射（`-p`）：容器的“门卫”机制
> **问题：** 容器有自己的内网 IP（如 `172.17.0.2`），电脑（宿主机）和外部网络无法直接访问它。
> **解法：** 端口映射（`-p 宿主机端口:容器端口`），在宿主机网卡上开一道门。

- **`-p 3306:3306`**：访问 `localhost:3306` → 转发到容器内 `3306`。
- **`-p 3307:3306`**：访问 `localhost:3307` → 转发到容器内 `3306`（适用于运行多个 MySQL 实例）。
- **容器间通信**：如果两个容器在同一个 Docker 网络，它们直接用 **容器名:容器端口**（如 `mysql-db:3306`）通信，**不用**经过映射端口。

## 3. 数据持久化（`-v`）：把容器数据存到硬盘上
> **痛点：** 容器删了，里面的数据（如 MySQL 表数据）就没了。
> **解法：** 挂载卷（Volume），把容器目录映射到宿主机。

### ⚠️ 关键区分（绝对重点！）
`-v` 后面跟着的路径有两种写法，含义完全不同：

| 写法示例 | 名称 | 数据存哪？ | 适用场景 |
| :--- | :--- | :--- | :--- |
| **`-v /home/mydata:/var/lib/mysql`** | **绑定挂载（绝对路径）** | 你指定的文件夹（如 `/home/mydata`） | 想自己管理文件位置，或想直接复制文件进去 |
| **`-v mysql-data:/var/lib/mysql`** | **命名卷（卷名）** | Docker 专属地盘（`/var/lib/docker/volumes/...`） | **强烈推荐**。权限自动搞定，不会报错，跨平台兼容 |

- **固定公式**：**右边永远是容器内的目录**（`/var/lib/mysql`）；**左边是宿主机的存放处**（路径或卷名）。

## 4. MySQL 容器的“启动原理”（为什么启动要等几秒？）
> **疑问：** Nginx 瞬间启动，为什么 MySQL 要等几秒？
> **原因：** MySQL 镜像里不仅有一个二进制文件，还有一个 **“安装引导脚本”**（`docker-entrypoint.sh`）。

**执行流程：**
1. Docker 执行脚本，而非直接运行 `mysqld`。
2. 脚本检查 `/var/lib/mysql` 目录是否为空。
   - **空**（第一次启动） → 执行 `mysql_install_db` 初始化系统表 → 启动临时服务 → 设置 `root` 密码（读 `-e` 变量） → 关闭临时服务。
   - **非空**（重启） → 跳过初始化。
3. 脚本最后执行 `exec mysqld`，把控制权交给 MySQL 主进程。
4. 服务正式启动，监听 3306 端口。

## 5. 救急命令（肌肉记忆）
| 场景 | 命令 |
| :--- | :--- |
| **后台启动 MySQL** | `docker run -d --name mysql-test -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 -v mysql-data:/var/lib/mysql mysql:8.0` |
| **查看运行中的容器** | `docker ps` |
| **查看容器日志（排错）** | `docker logs mysql-test` |
| **进入容器内部命令行** | `docker exec -it mysql-test /bin/bash` |
| **进入容器内部的 MySQL** | `docker exec -it mysql-test mysql -uroot -p123456` |
| **查看卷的真实存放路径** | `docker volume inspect mysql-data` |
| **删除容器（强制）** | `docker rm -f mysql-test`（注意：卷 `mysql-data` 不会自动删除，数据还在） |

## 6. 进阶技法：`docker-compose.yml`（告别长命令）
创建一个 `docker-compose.yml` 文件，把参数写进去，以后只需要 `docker-compose up -d` 就能启动。

```yaml
version: '3'
services:
  mysql:
    image: mysql:8.0
    container_name: mysql-test
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    ports:
      - "3307:3306"          # 宿主机3307映射到容器3306
    volumes:
      - mysql-data:/var/lib/mysql  # 命名卷持久化
    restart: always

volumes:
  mysql-data:   # 声明这个卷名，Docker会自动创建
```

---

### 💡 今日灵魂总结（三句话记住今天的内容）
1. **Docker 只是个“送货员”**：它不认识 MySQL，只知道拿着镜像名去执行里面提前写好的脚本。
2. **端口映射是给外人看的**：容器内部自己玩不用映射，外面访问才需要 `-p`。
3. **数据卷是“保险柜”**：`-v 名字:/var/lib/mysql` 让容器删了数据还在，且永不报错。

---

