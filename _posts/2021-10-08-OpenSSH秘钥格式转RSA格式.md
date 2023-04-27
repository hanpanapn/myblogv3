---
layout: post
title: OpenSSH秘钥格式转RSA格式
tags : Linux
stickie: false
---

在使用秘钥方式连接云服务器时，选需要使用```ssh-keygen -o ```生成秘钥，格式是OpenSSH的格式，而一些其他软件需要的是RSA格式

此方法可直接生成RSA格式秘钥，不用转换：```ssh-keygen -m PEM -t rsa -b 4096 ```

特别是[https://cloud.tencent.com/developer/article/1720500](https://cloud.tencent.com/developer/article/1720500) 这篇文章讲“使用GithubActions自动部署应用到自己的服务器（ECS）”

github中需要设置服务器的私钥，就是RSA格式

![image](https://user-images.githubusercontent.com/15027167/136510520-ff8a7cae-277f-4f58-ba10-4019b3e9862e.png)
