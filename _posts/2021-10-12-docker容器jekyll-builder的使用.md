---
layout: post
title: docker容器jekyll-builder的使用
category : 
tags : Linux
stickie: false
seo_keywords: docker 容器 jekyll 博客 搭建 网站 独立站 静态
seo_description: docker容器jekyll/builder的使用
---

# 背景

在用GitHub提供的Pages搭建博客时，一直不明白是怎么吧*.md文件编译成html，并提供web服务的，后来在Actions中看到了编译脚本：

```bash
docker run \
-v ${{ github.workspace }}:/srv/jekyll -v ${{ github.workspace }}/_site:/srv/jekyll/_site \
jekyll/builder:latest /bin/bash -c "chmod -R 777 /srv/jekyll && jekyll build --future"
```

可以看到编译用的就是jekyll/builder:latest 这个镜像，通过查资料（[https://github.com/envygeeks/jekyll-docker#readme](https://github.com/envygeeks/jekyll-docker#readme)） 还找到了同类型的docker镜像：

Image Types：
- jekyll/jekyll: Default image.
- jekyll/minimal: Very minimal image.
- jekyll/builder: Includes tools.

下面就来研究一下镜像 ```jekyll/builder``` 到底怎么使用。


# 作用 

docker镜像jekyll/builder我理解是包含了一些工具的jekyll构建环境，可以把本地中的jekyll源文件编译成静态html

# 学习过程

Actions中的默认构建脚本大概意思就是用Docker镜像 ```jekyll/builder:latest``` 把/srv/jekyll 目录下的文件编译到/srv/jekyll/_site中，实际存放的位置应该是： ${{ github.workspace }}:/srv/jekyll，编译的具体命令是：

```bash
chmod -R 777 /srv/jekyll && jekyll build --future
```


接下来就摆脱github,单独在我本地的docker环境中运行镜像jekyll/builder

```
mkdir /home/hanpanpan92/site_source && /home/hanpanpan92/site_source/_site

//site_source文件夹中建立index.md 文件

docker pull jekyll/builder

docker run -v /home/hanpanpan92/site_source:/srv/jekyll -v /home/hanpanpan92/site_source/_site:/srv/jekyll/_site jekyll/builder:latest /bin/bash -c "chmod -R 777 /srv/jekyll && jekyll build"
```

在执行完 docker run 。。。这个命令后确定把index.md文件编译到了/home/hanpanpan92/site_source/_site文件夹下，但是并没有生成html格式，依旧是.md格式文件，什么情况？
然后把我这个博客的源码 放到了site_source目录下再次执行docker run 。。。发现_site文件夹下确实生成了html静态文件和对应的js images css等，那可能就是配置的问题了。

在我博客源码中运用“强力删除拍错法”得到最精简的目录结构：

![image](https://user-images.githubusercontent.com/15027167/136945604-aedb45ac-2c65-41d8-b748-ba530fd12cc5.png)

运行 docker run  xxxx 后成功吧md文件编译到了_site文件夹中

![image](https://user-images.githubusercontent.com/15027167/136946104-014befbd-b106-4919-bf4b-b43dc86bb5e2.png)

最简源码：
[site_source.zip](https://github.com/hanpanapn/MyBlog/files/7329423/site_source.zip)

可以看出它是把更目录下的index.md文件解析后，由布局文件_layouts/default.html生成最终的html,并存到_site中，注意布局文件是html格式而不是md,理解原理后就可以参考jekyll官网写更多功能，比如建单页面，列表页，列表页中的分页，文章分类这样的功能

官网文档：[http://jekyllcn.com/docs/home/](http://jekyllcn.com/docs/home/)

现在我们就可以本地搭建jekyll开发环境，使用Docker镜像编译，这样我们本地机器就不用安装jekyll相关的工具了，因为都在docker中(实际是我在linux中怎么也安装不成功jekyll的开发环境。。。)

思维在飞翔一下：我可以提交代码到github,利用github 的 Actions自定义构建过程，把构建产物_site中的静态文件发布到自己的服务器中，完美!


# 总结

Docker镜像jekyll/builder的作用，就是jekyll的编译工具，免去在本地，或者服务器安装各种工具，只要按照它规定的目录结构编写源文件，就可以很方便的编译成静态html

更多jekyll的规则就去官网查看吧： [http://jekyllcn.com/docs/home/](http://jekyllcn.com/docs/home/)

# 知识点

jekyll/builder:latest 这个镜像运行后并没有像其他镜像一样 能用 docker ps -a 查询到，感觉它就想一个编译工具，运行它，把A变成B,然后自己退出。好像和Docker容器的两种运行模式(Foreground、Detached)有关，这还没具体了解





