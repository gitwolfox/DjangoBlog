# docker
## docker and compose安装
### docker
```
curl -fsSL https://get.docker.com | bash -s docker
```
### docker-compose
```
curl -fsSL https://get.docker.com | bash -s docker
curl -L "https://github.com/docker/compose/releases/download/v2.14.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```
## docker卸载
```
apt-get autoremove docker docker-ce docker-engine  docker.io  containerd runc
dpkg -l | grep docker
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P # 删除无用的相关的配置文件
apt-get autoremove docker-ce-* #删除插件
rm -rf /etc/systemd/system/docker.service.d
rm -rf /var/lib/docker
```
## docker 常见命令
docker pull image  
docker run -it -d -p 8080:80 --name nginxweb -v ~/site-content:/usr/share/nginx/html nginx   
sudo docker exec -it nginxweb /bin/bash  
### 重启服务  
systemctl stop docker.service  
systemctl start docker.service  
docker start nginxweb  
docker attach nginxweb  
## 删除所有容器镜像
```
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)
docker rmi $(docker images -q)
```
## nginx docker 挂载配置
createdocker.sh
```
docker run \
  --name nginx81 \
  -d -p 81:80 \
  -v /home/jerry/docker_project/dockerSiteContent:/usr/share/nginx/html \
  -v /home/jerry/docker_project/nginx/nginx.conf:/etc/nginx/nginx.conf \
  -v /home/jerry/docker_project/nginx/conf.d:/etc/nginx/conf.d \
  -v /home/jerry/docker_project/nginx/log:/var/log/nginx \
  nginx
  ```
## dockerfile
create Dockerfile
```
FROM nginx:latest
COPY ./index.html /usr/share/nginx/html/index.html
```
docker build -t webserver .  
## 挂载

## 聚合多个docker
### 下载镜像
docker pull image1  
docker pull image2  
### 然后使用docker history来取得构建这两个容器时运行的命令
docker history --no-trunc=true image > image1-dockerfile  
docker history --no-trunc=true image2 > image2-dockerfile  
接下来打开这两个文件，你可以看到每个镜像的命令堆栈。这是因为Docker镜像通过层（阅读更多）的方式来构建。即你在Dockerfile中键入的每一个命令所构建的新镜像，都是在之前的命令产生的镜像之上。所以你可以对镜像进行逆向工程。

### 限制
你不能对镜像进行反向工程的唯一场景，是镜像的维护者在他的Dockerfile中使用了ADD或COPY命令。你会看到这样一行：
ADD file:1ac56373f7983caf22 或  
ADD dir:cf6fe659e9d21535844 