## 、系统信息查询（了解系统状态）

|命令|功能描述|示例|
|---|---|---|
|`uname -a`|查看内核版本、系统架构等完整信息|`uname -a`（输出：Linux kali 6.1.0-kali9-amd64 ...）|
|`lsb_release -a`|查看 Kali 系统版本（官方推荐）|`lsb_release -a`（输出：Distributor ID: Kali）|
|`hostname`|查看主机名|`hostname`（默认主机名：kali）|
|`whoami`|查看当前登录用户|`whoami`（普通用户输出：kali，root 输出：root）|
|`hostname -I`|查看本机 IP 地址（快速方式）|`hostname -I`（输出：192.168.1.105 10.0.0.5）|
|`df -h`|查看磁盘空间使用情况（-h 人性化显示）|`df -h`（查看根目录、家目录占用）|
|`free -h`|查看内存 / 交换分区使用情况|`free -h`（输出：总内存、已用、空闲）|

## 二、目录与文件操作（最常用核心）

### 1. 目录导航

|命令|功能描述|示例|
|---|---|---|
|`cd 路径`|切换目录（绝对路径 / 相对路径）|`cd /home/kali`（绝对路径）、`cd ../docs`（相对路径，返回上一级再进 docs）|
|`cd ~`|切换到当前用户家目录（默认 `/home/kali`）|`cd ~`|
|`cd ..`|切换到上一级目录|`cd ..`（从 `/home/kali` 到 `/home`）|
|`cd -`|切换到上一次所在目录|`cd -`（在两个目录间切换）|
|`pwd`|显示当前所在目录的绝对路径|`pwd`（输出：/home/kali/Documents）|

### 2. 文件 / 目录查看

|命令|功能描述|示例|
|---|---|---|
|`ls`|列出当前目录文件 / 目录（默认不显示隐藏文件）|`ls`|
|`ls -l`（缩写 `ll`）|详细列出（权限、所有者、大小、修改时间）|`ll /home`（查看 /home 目录详细信息）|
|`ls -a`|显示所有文件（含隐藏文件，以 `.` 开头）|`ls -a ~`（查看家目录隐藏文件，如 `.bashrc`）|
|`ls -lh`|详细列出 + 人性化显示文件大小（KB/MB/GB）|`ls -lh /var/log`（查看日志文件大小）|
|`cat 文件名`|查看文件内容（适合小文件）|`cat /etc/passwd`（查看系统用户列表）|
|`more 文件名`|分页查看大文件（按 Enter 翻行，q 退出）|`more /var/log/syslog`|
|`less 文件名`|分页查看大文件（支持上下键翻行，q 退出）|`less /etc/shells`|
|`head -n 数字 文件名`|查看文件前 N 行|`head -10 /etc/passwd`（查看前 10 个用户）|
|`tail -n 数字 文件名`|查看文件后 N 行（-f 实时跟踪更新）|`tail -20 /var/log/kern.log`（查看后 20 行）、`tail -f /var/log/apache2/access.log`（实时监控 Apache 日志）|

### 3. 文件 / 目录创建 / 删除 / 复制 / 移动

|命令|功能描述|示例|
|---|---|---|
|`touch 文件名`|创建空文件（或更新文件修改时间）|`touch test.txt`、`touch 2025-11-04.log`|
|`mkdir 目录名`|创建空目录|`mkdir tools`（创建工具目录）|
|`mkdir -p 路径`|递归创建多级目录（父目录不存在则自动创建）|`mkdir -p tools/nmap/scans`（创建三级目录）|
|`rm 文件名`|删除文件（需确认，无法恢复）|`rm test.txt`（提示：rm: remove regular empty file 'test.txt'?）|
|`rm -f 文件名`|强制删除文件（无提示）|`rm -f old.log`|
|`rm -r 目录名`|递归删除目录及内容（需确认）|`rm -r tools`（删除 tools 目录及所有子文件）|
|`rm -rf 目录名`|强制递归删除（无提示，**极其危险**）|禁止执行 `rm -rf /`（删除整个系统）！示例：`rm -rf unused_dir`|
|`rmdir 目录名`|仅删除空目录（目录非空则报错）|`rmdir empty_dir`|
|`cp 源文件 目标路径`|复制文件 / 目录|`cp test.txt ~/Documents`（复制文件到文档目录）|
|`cp -r 源目录 目标路径`|递归复制目录及内容|`cp -r tools ~/backup`（复制 tools 目录到备份目录）|
|`mv 源文件 目标路径`|移动文件 / 目录（或重命名）|`mv test.txt ~/Downloads`（移动文件）、`mv old.txt new.txt`（重命名文件）|

## 三、权限管理（Kali 核心操作，渗透测试常用）

Kali 中文件 / 目录权限分为 **读（r=4）、写（w=2）、执行（x=1）**，对应三类用户：所有者（u）、组用户（g）、其他用户（o）。

|命令|功能描述|示例|
|---|---|---|
|`chmod 权限 文件名`|修改文件 / 目录权限（数字 / 符号方式）|1. 数字方式：`chmod 755 script.sh`（u=rwx, g=rx, o=rx，脚本可执行）；2. 符号方式：`chmod +x exploit.py`（给所有用户添加执行权限，渗透脚本常用）、`chmod 644 config.txt`（u=rw, g=r, o=r，普通文件）|
|`chown 所有者:组 文件名`|修改文件 / 目录的所有者和组|`sudo chown kali:kali test.txt`（将文件所有者和组改为 kali）、`sudo chown root:root /opt/tools`（改为 root 所有）|
|`sudo 命令`|以 root 权限执行命令（Kali 默认非 root 用户，需 sudo 获取管理员权限）|`sudo apt update`（更新软件源）、`sudo su`（切换到 root 用户）|
|`su 用户名`|切换到指定用户（su 无参数默认切换到 root）|`su testuser`（切换到 testuser）、`su -`（切换到 root 并加载 root 环境变量）|

## 四、包管理（安装 / 更新 / 卸载软件，Kali 基于 APT）

Kali 官方软件源包含大量渗透测试工具（如 nmap、metasploit），核心包管理命令为 `apt`（替代旧版 `apt-get`）：

|命令|功能描述|示例|
|---|---|---|
|`sudo apt update`|更新软件源索引（获取最新软件版本信息）|必须先执行此命令，再升级 / 安装软件|
|`sudo apt upgrade`|升级已安装软件（不删除旧依赖）|`sudo apt upgrade -y`（-y 自动确认升级，无需手动输入 y）|
|`sudo apt full-upgrade`|升级软件（必要时删除冲突依赖）|`sudo apt full-upgrade -y`（推荐定期执行，保持系统最新）|
|`sudo apt install 软件名`|安装软件|`sudo apt install nmap`（安装端口扫描工具 nmap）、`sudo apt install metasploit-framework`（安装 MSF 框架）|
|`sudo apt remove 软件名`|卸载软件（保留配置文件）|`sudo apt remove nmap`|
|`sudo apt purge 软件名`|彻底卸载软件（删除配置文件）|`sudo apt purge metasploit-framework`|
|`sudo apt autoremove`|自动删除无用依赖包（清理系统）|`sudo apt autoremove -y`|
|`apt search 关键词`|搜索软件包|`apt search port scan`（搜索端口扫描相关工具）|

## 五、网络基础命令（渗透测试前置操作）

|命令|功能描述|示例|
|---|---|---|
|`ip addr`（替代 `ifconfig`）|查看网卡信息、IP 地址（推荐）|`ip addr`（输出 eth0/wlan0 的 IP、MAC 地址）|
|`ifconfig`|查看 / 配置网卡（需安装 `net-tools`：`sudo apt install net-tools`）|`ifconfig wlan0`（查看无线网卡信息）|
|`ping 目标IP/域名`|测试网络连通性（-c 指定包数，避免无限 ping）|`ping -c 4 www.baidu.com`（发送 4 个数据包测试百度连通性）|
|`ss -tuln`（替代 `netstat`）|查看监听的 TCP/UDP 端口（快速）|`ss -tuln`（列出所有开放的端口，如 22 ssh、80 http）|
|`netstat -tuln`|查看监听端口（需安装 `net-tools`）|`netstat -tuln`|
|`curl 网址`|发送 HTTP 请求（查看网页源码 / 测试接口）|`curl https://www.kali.org`（查看 Kali 官网源码）、`curl -I https://www.baidu.com`（查看响应头）|
|`wget 下载链接`|下载文件（后台下载：-b，断点续传：-c）|`wget https://example.com/exploit.zip`（下载漏洞利用脚本）、`wget -c https://large-file.iso`（断点续传大文件）|
|`route -n`|查看路由表（网关、目标网络）|`route -n`（输出默认网关：0.0.0.0 192.168.1.1）|

## 六、用户与组管理（创建 / 删除用户）

|命令|功能描述|示例|
|---|---|---|
|`useradd -m 用户名`|创建用户并自动创建家目录（/home/ 用户名）|`sudo useradd -m testuser`（创建 testuser 用户）|
|`passwd 用户名`|设置 / 修改用户密码（root 或当前用户可执行）|`sudo passwd testuser`（给 testuser 设密码，输入时不显示）|
|`userdel -r 用户名`|删除用户并删除家目录（-r 递归删除）|`sudo userdel -r testuser`（彻底删除 testuser）|
|`groupadd 组名`|创建用户组|`sudo groupadd hackers`（创建 hackers 组）|
|`usermod -aG 组名 用户名`|将用户添加到组（-aG 追加组）|`sudo usermod -aG sudo testuser`（给 testuser 添加 sudo 权限）|
|`id 用户名`|查看用户的 UID、GID 及所属组|`id kali`（输出：uid=1000 (kali) gid=1000 (kali) groups=1000 (kali),27 (sudo)...）|

## 七、进程管理（查看 / 结束进程）

|命令|功能描述|示例||
|---|---|---|---|
|`ps aux`|查看所有进程（a = 所有用户，u = 详细信息，x = 无终端进程）|`ps aux|grep nmap`（查找 nmap 相关进程）|
|`top`|实时监控进程（按 CPU / 内存排序，q 退出）|`top`（默认按 CPU 使用率排序，按 M 切换内存排序）||
|`kill 进程ID`|结束进程（默认信号 15，正常终止）|`kill 1234`（结束 PID 为 1234 的进程）||
|`kill -9 进程ID`|强制结束进程（信号 9，无法拒绝）|`kill -9 5678`（强制结束无响应进程）||
|`pkill 进程名`|按进程名结束进程|`pkill firefox`（结束所有火狐浏览器进程）||

## 八、实用工具与技巧（提高效率）

|命令 / 操作|功能描述|示例|
|---|---|---|
|`clear`（快捷键 Ctrl+L）|清空终端屏幕|`clear`|
|`history`|查看历史执行命令（默认保存 1000 条）|`history`（输出所有历史命令）、`!100`（执行第 100 条历史命令）|
|`tab 键`|自动补全命令 / 文件名（避免输错）|输入 `cd /ho` + tab → 自动补全为 `cd /home`|
|`man 命令`|查看命令帮助文档（详细用法）|`man ls`（查看 ls 命令所有选项）、`man nmap`（查看 nmap 工具用法）|
|`find 路径 -name "文件名"`|查找文件（按名称）|`find / -name "exploit.py"`（从根目录查找 exploit.py）、`find ~ -name "*.txt"`（查找家目录下所有.txt 文件）|
|`grep 关键词 文件名`|在文件中搜索关键词（-i 忽略大小写，-n 显示行号）|`grep -in "root" /etc/passwd`（查找 /etc/passwd 中含 root 的行，忽略大小写并显示行号）|

## 九、关键注意事项（新手避坑）

1. 1.
    
    **权限控制**：Kali 默认登录用户是 `kali`，需用 `sudo` 执行管理员操作（如安装软件、修改系统文件），避免直接用 root 登录（风险高）。
    
2. 2.
    
    `**rm -rf**` **慎用**：此命令强制删除文件 / 目录且无法恢复，尤其禁止执行 `sudo rm -rf /`（会格式化整个系统）。
    
3. 3.
    
    **软件源更新**：安装工具前务必先执行 `sudo apt update`，否则可能安装旧版本或提示 “软件包不存在”。
    
4. 4.
    
    **隐藏文件**：Linux 中以 `.` 开头的文件是隐藏文件（如 `.bashrc`、`.ssh`），需用 `ls -a` 查看。
    
5. 5.
    
    **路径区分**：绝对路径从 `/` 开始（如 `/home/kali`），相对路径从当前目录开始（如 `../tools`）。
    

掌握以上命令后，可满足 Kali Linux 日常操作、工具安装、基础网络排查需求，为后续渗透测试学习打下基础。如果需要特定场景（如 MSF、Nmap 工具使用）的命令，可进一步补充说明！

编辑分享

介绍一下Kali Linux的历史和背景

常用的网络基础命令有哪些？

如何在Kali Linux中安装软件？