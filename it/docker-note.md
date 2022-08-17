
# 本地
## 镜像
1. 创建镜像
```
docker build -t xxx/yyy:1.0.0  dir
```

2. 查看本地镜像列表
```
[root@xxx ~]# docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
xxx/yyy                        1.0.0               d10d3bd9cf54        1 years ago         354MB
```

3. 删除镜像
```
docker rmi d10d3bd9cf54
```

## 容器

### 启动容器
```
docker run -itd --privileged -v 你的开发目录:/root --net=host --name xxx-compile-suziliu mirrors.website.com/todacc/xxx-compile:0.1.7 /usr/sbin/init
docker run -itd --privileged -v /data/docker:/root --net=host --name xxx mirrors.website.com/todacc/xxx-compile:0.1.7 /usr/sbin/init
```


1. 查看所有的容器
```
[root@xxx ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
5993da92fe87        469034cc65ce        "/bin/sh -c 'yum -y …"   10 minutes ago      Exited (1) 10 minutes ago                       cool_kare
a7807b0587c3        469034cc65ce        "/bin/sh -c 'yum -y …"   15 minutes ago      Exited (1) 15 minutes ago                       awesome_pike
9b55a67431da        469034cc65ce        "/bin/sh -c 'yum -y …"   16 minutes ago      Exited (1) 16 minutes ago                       friendly_tereshkova
3d175dc2d846        469034cc65ce        "/bin/sh -c 'yum -y …"   20 minutes ago      Exited (1) 20 minutes ago                       beautiful_faraday
```
不带参数只能看到正在运行的容器

2. 在新容器中运行命令
交互模式下运行
```
docker run -i -t mirrors.xxx.com/xxx:1.0.0 bash
```

3. 在已有的容器里面运行命令 
```
docker exec -it a7807b0587c3 bash
```

4. 停止正在运行的容器
```
docker stop xxx
```

5. 启动容器
```
docker start xxx
```

6. 删除容器
```
docker rm a7807b0587c3
```

7. 提交更新
```
docker commit a7807b0587c3
```

8. 拷贝
```
docker cp mycontainer:/opt/testnew/file.txt /opt/test/
docker cp /opt/test/file.txt mycontainer:/opt/testnew/
```

9. 创建
指定6个核心运行docker镜像，并且拥有权限，设置内存大小
```
docker create --name xxx -m 8g --cpus 6 --cpuset-cpus 0,8-12 --privileged --shm-size 15g -i -t xxx/yyy:1.0.0
```

# 远程
1. 登录
```
docker login --username xxx  --password xxxx www.xxx.com
```

2. 拉取镜像
```
docker pull www.xxx.com/NAMESPACE/REPO[:TAG]
```
NAMESPACE是命名空间，REPO是仓库，TAG是版本号，可选

3. 推送镜像
```
docker tag [image_id] www.xxx.com/NAMESPACE/REPO:latest
docker push www.xxx.com/NAMESPACE/REPO:latest
```

# 如何查看自己能使用的核心资源
```
cat /sys/fs/cgroup/cpuset/cpuset.cpus
```
或者
```
cat /sys/fs/cgroup/cpuset$(cat /proc/1/cpuset)/cpuset.cpus
``
