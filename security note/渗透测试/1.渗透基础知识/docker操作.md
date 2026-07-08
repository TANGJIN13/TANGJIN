### 一、docker镜像操作
1.列出镜像
```
docker image ls -a
docker images -a
docker images
```

2.拉取镜像
docker image pull 命令，后跟镜像的名称和标签,
例如，拉取 hello-world 镜像:
```
docker image pull docker.io/library/hello-world:latest
```

docker.io:这是 Docker Hub 的域名，是 Docker 官方镜像仓库的地址。通常在不指定仓库地址的
情况下，Docker 会默认使用 docker.io。
```
docker image pull library/hello-world
```

library:这是 Docker Hub 上的默认命名空间，用于存放官方提供的镜像。使用 library 命名空间
可以确保你拉取的是官方维护的镜像。由于 Docker 官方的镜像默认位于 library 组下，因此可以
省略 library
```
docker image pull hello-world
```

3.删除镜像
指定镜像名称
```
docker image rm hello-world
```
指定镜像ID
```
docker rmi feb5d9fea6a5
docker rmi feb5//和上面一样
```

### 二、docker容器操作
1.创建并运行容器
格式如下：
```
docker run [option] image_name [command] 
```
option 是可选参数
image_name 是镜像的名称
command 是在容器启动时执行的命令

2.交互式容器
创建一个交互式容器，命名为 mycentos，并允许用户登录并执行命令
```
docker run -it --name=mycentos centos /bin/bash
```

3.守护式容器
创建一个守护式容器，命名为 mycentos2，容器在后台运行，不会自动登录:
```
docker run -dit --name=mycentos2 centos
```
![[Pasted image 20241209115808.png]]

4.进入容器
进入正在运行的容器：
```
docker exec -it container_name_or_id command
```
exec:用于在已经运行的容器中执行命令
-i:以交互模式运行容器。
-t:分配一个伪终端，容器启动后会进入容器命令行
container_name_or id 表示容器名或容器id
command 表示进入容器后执行的第一个命令
例如，进入名为 mycentos2 的容器并执行 /bin/bash，打开一个Bash shell终端
```
docker exec -it mycentos2 /bin/bash
```
例子：进入uploads靶场容器，修改文件
![[Pasted image 20241209115607.png]]
5.查看容器
```
docker ps
docker ps -a
```

6.停止和启动容器
```
docker stop 容器名或容器id
```

```
docker start 容器名或容器id
```

```
//强制停止运行中的容器
docker kill 容器名或容器id
```

7.删除容器
```
docker container rm 容器名或容器id

docker rm 容器名或容器id
```

### 三、容器保存为镜像

如果需要把更改过的容器创建为一个新的镜像，可以使用 docker commit 命令:
```
docker commit 容器名 镜像名
```

例如，将名为 mycentos3 的容器转换为一个新的镜像 mycentos3:
```
docker commit mycentos3 mycentos3
```

### 四、docker镜像备份
要备份镜像，可以使用 docker save 命令将镜像保存为一个文件:
```
docker save -o 保存的文件名 镜像名
```

这将创建一个包含镜像及其所有层的归档文件。
例如，备份 mycentos3 镜像:
```
docker save -o ./mycentos.tar mycentos3
```

### 五、docker镜像迁移
备份的镜像文件可以被拷贝到其他机器上。
在目标机器上，使用 docker load 命令来加载镜像:
```
docker load -i 文件名
```

例如，加载之前备份的 mycentos.tar 文件:
```
docker load -i ./mycentos.tar
```

