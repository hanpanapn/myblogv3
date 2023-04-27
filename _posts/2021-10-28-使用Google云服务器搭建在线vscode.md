---
title: 使用Google云服务器搭建在线vscode
category : 服务器
tags : Linux 
stickie: 
seo_keywords: 在线 vscode 云服务器 搭建 IDE online web
seo_description: 使用Google云服务器搭建在线vscode
---

严格来说不应该叫vscode,应该叫code-server 

开源项目：[https://github.com/linuxserver/docker-code-server](https://github.com/linuxserver/docker-code-server)

Docker镜像：[https://hub.docker.com/r/linuxserver/code-server](https://hub.docker.com/r/linuxserver/code-server)

自从会用Docker后，感觉太爽了~  下面就是基于Docker部署code-server

实现功能：

通过 https://code-server.hpanpan.cc/ 打开，输入密码后登录，登录后就像用本地vscode一样啦，需要注意一点是扩展插件官方说无法连接到在线库，但我试了是可以的如下：

![image](https://user-images.githubusercontent.com/15027167/139243905-acce2bfb-be0b-475b-a254-725b25159ea4.png)

Docker 安装方法这里不讲，很多教程

下面是docker  code-server 镜像启动代码：

```bash
docker run -d \
  --name=code-server \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Shanghai \
  -e PASSWORD=123456 \
  -e SUDO_PASSWORD=123456  \
  -e PROXY_DOMAIN=code-server.hpanpan.cc   \
  -p 8000-9000:8000-9000 \
  -v /home/code-server/config:/config \
  --restart unless-stopped \
  lscr.io/linuxserver/code-server
 ```
 
 其中的``` -p 8000-9000:8000-9000``` 说一下，code-server 默认端口是8443，使用过程中可能需要自定义启动程序向外暴露接口，比如 npm run dev
 
 程序中启动了8001端口，所以用docker批量端口映射来满足开发需要。启动后就可以用 http://34.92.198.141:8443  访问了
 
 然后如何 访问 https://code-server.hpanpan.cc 打开code-server 是属于域名解析方面的事情，下面是我的nginx配置：
 
 ```nginx
 server {
        listen       443 ssl;
        server_name  code-server.hpanpan.cc;
        proxy_set_header  Host  $host;
        proxy_set_header  X-real-ip $remote_addr;
        proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
        add_header 'Access-Control-Allow-Credentials' 'true';
        ssl_certificate_key  xxx.key;
        ssl_certificate  xxx.pem;
        location / {
              proxy_pass http://code-server.hpanpan.cc:8443; #这里不要用http://localhost:8443,访问时会报错的
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection upgrade;
              proxy_set_header Accept-Encoding gzip;
        }
}
server {
    listen 80;
    server_name code-server.hpanpan.cc;
    rewrite ^(.*)$ https://code-server.hpanpan.cc;
}
 ```
 
 ![image](https://user-images.githubusercontent.com/15027167/139246438-99ab0909-9229-4d05-baf9-9e3006ed40a4.png)



![image](https://user-images.githubusercontent.com/15027167/139241466-289c4fba-0878-4033-af8c-60905343628d.png)

#### 遇见的问题

code-server中无法使用ssh 方式clone仓库，在ssh直连服务器的命令行中是可以的，可能的原因是docker启动添加了用户组 ```  -e PUID=1000 -e PGID=1000  ``` 还未确定

在code-server命令行中执行 ```sudo npm install xxx --save-dev``` 时，报错：make: command not found，这是因为这个docker镜像系统中没有安装make等常用命令

需要安装的有：``` sudo apt install   gcc automake autoconf libtool make cmake g++ ```

