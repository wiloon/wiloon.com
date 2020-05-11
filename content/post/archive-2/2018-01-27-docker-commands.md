---
title: docker commands
author: wiloon
type: post
date: 2018-01-27T08:35:04+00:00
url: /?p=11796
categories:
  - Uncategorized

---
<pre><code class="language-bash line-numbers">列出所有的容器 ID
docker ps -aq
停止所有的容器
docker stop $(docker ps -aq)
删除所有的容器
1
docker rm $(docker ps -aq)
删除所有的镜像
1
docker rmi $(docker images -q)
复制文件
1
2
docker cp mycontainer:/opt/file.txt /opt/local/
docker cp /opt/local/file.txt mycontainer:/opt/
</code></pre>

### docker port

docker port :列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口。
  
docker port [OPTIONS] CONTAINER [PRIVATE_PORT[/PROTO]]

### docker pod log

/var/log/containers/
  
/var/log/pods/<container id>/container-0
  
/var/lib/docker/containers/<container id>

### install

<https://blog.wiloon.com/?p=12943>

### image

<pre><code class="language-bash line-numbers"># 列出本机的所有 image 文件
docker image ls
#显示包括中间层镜像在内的所有镜像
docker image ls -a
# 显示虚悬镜像(dangling image)
docker image ls -f dangling=true
docker image ls --digests
# 删除 image 文件
docker image rm [image id]

# 删除未使用的映像
docker image prune

docker rmi f8ab12e03d53
Error response from daemon: conflict: unable to delete f8ab12e03d53 (must be forced) - image is referenced in multiple repositories
docker rmi 192.168.0.1/you/tom:1.0.8
</code></pre>

### install

<pre><code class="language-bash line-numbers">docker ps -s
</code></pre>

<pre><code class="language-bash line-numbers">sudo pacman -S docker

# docker
docker run \
-d \
--name redis \
-p 6379:6379 \
-v /etc/localtime:/etc/localtime:ro \
--restart=always \
redis
</code></pre>

### network

<pre><code class="language-bash line-numbers">docker network ls
docker network inspect 网络ID

</code></pre>

### volume

<pre><code class="language-bash line-numbers">docker volume ls
docker volume create --name influxdb-config
docker volume rm influxdb-config
</code></pre>

容器创建后不能再新增volume, 可以先commit到镜像， 再创建新的容器。

### 时区问题

<pre><code class="language-bash line-numbers">-v /etc/localtime:/etc/localtime:ro
</code></pre>

### 监控

<pre><code class="language-bash line-numbers">docker stats
</code></pre>

<https://www.cnblogs.com/sparkdev/p/7821376.html>

### logs

<pre><code class="language-bash line-numbers">docker logs --since 10s -f influxdb
</code></pre>

<pre><code class="language-bash line-numbers">docker inspect xxx
docker container update --restart=no &lt;containername&gt;
docker container update --restart=always &lt;containername&gt;
</code></pre>

### docker build

<pre><code class="language-bash line-numbers"># 构建镜像
# docker build [选项] &lt;上下文路径/URL/-&gt;
# --tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。

docker build -t dnsmasq:v1.0.0 .
docker tag dnsmasq:v1.0.0 swr.cn-south-1.myhuaweicloud.com/{组织名称}/dnsmasq:v1.0.0
docker push swr.cn-south-1.myhuaweicloud.com/{组织名称}/dnsmasq:v1.0.0
docker pull swr.cn-south-1.myhuaweicloud.com/{组织名称}/dnsmasq:v1.0.0

#  从 Git repo 中构建
docker build https://github.com/twang2218/gitlab-ce-zh.git#:11.1

# -f 指定Dockerfile路径
docker build -f /path/to/Dockerfile .
</code></pre>

## docker run

创建一个新的容器并运行一个命令
  
docker run \[OPTIONS] IMAGE [COMMAND\] \[ARG&#8230;\]

<pre><code class="language-bash line-numbers">docker run -it --rm ubuntu bash
# -i, --interactive=false   打开STDIN，用于控制台交互
# -t, --tty=false           分配tty设备，该可以支持终端登录，默认为false
# -t -a stdout              Outputs the container logs on the standard output
# -P, --publish-all=false   Docker自动分配一个未被使用的端口
# -v, --volume=[]           Bind mount a volume(挂载目录 -v /root:/opt/temp)
# -d, --detach=false        Run container in background and print container ID(后台运行)并返回容器ID；
# --rm 容器退出后随之将其删除
# bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash
# --name="nginx-lb" : 为容器指定一个名称；
# -p 80:80 映射端口, hostPort:containerPort
# --privileged=true, 大约在0.6版，privileged被引入docker。使用该参数，container内的root拥有真正的root权限。
# --restart=always
# --cap-add=SYS_TIME
# -e, --env=[]                    Set environment variables(设置环境变量)
# --link 用来链接2个容器，使得源容器（被链接的容器）和接收容器（主动去链接的容器）之间可以互相通信，并且接收容器可以获取源容器的一些数据，如源容器的环境变量。--link &lt;name or id&gt;:alias 其中，name和id是源容器的name和id，alias是源容器在link下的别名。
# --cpus=2
# --cpuset-cpus="0" --cpu-shares=512

docker run \
-d \
--name name0 \
-p 2000:80/tcp \
-v /etc/localtime:/etc/localtime:ro \
--cpus 1 \
-m 1024m \
--restart=always \
image0_name

</code></pre>

<pre><code class="language-bash line-numbers"># 进入容器
docker exec -it webserver bash
docker exec -it --user=root foo bash

#查看存储层变化
docker diff webserver

#将容器保存为镜像
sudo docker commit \
    --author "wiloon" \
    --message "modify index" \
    webserver \
    nginx:v2

# 查看镜像内的历史记录
docker history nginx:v2

docker volume ls
docker volume rm

sudo pacman -S docker    # install docker
sudo systemctl start docker.service

sudo pacman -S docker-compose

# search image
sudo docker search [image name]

# 查看docker 版本
docker --version #查看版本
docker-compose --version #查看版本
docker-machine --version #查看版本
docker version #查看client和server端版本，并可以查看是否开启体验功能

sudo docker-compose up

docker ps                # 列出容器
docker stop container_id # 停止容器
docker rm container_id   # 删除容器

# network
docker network ls

# start dokcer
systemctl start docker
docker pull centos
docker image pull library/hello-world
docker container run hello-world
docker container kill [containID]

# start daemon manually
dockerd
docker images
docker search seanlo

docker container ls
docker container logs 244d1663f0b7 -f
docker container stop
docker container rm
docker container prune
docker run -dit -p 5000:5000 ubuntu
docker network create -d bridge my-net
</code></pre>

default volumn path /var/lib/docker/volumes/

https://segmentfault.com/a/1190000012063374
  
https://www.cnblogs.com/sparkdev/p/8052522.html
  
https://colobu.com/2018/05/15/Stop-and-remove-all-docker-containers-and-images/
  
https://cizixs.com/2017/08/04/docker-resources-limit/