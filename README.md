# 基于docker的django+nginx+uwsgi+postgresql部署实践
*目前根据这个项目文件已经实验通过，后续会补上详细步骤*

## 前言

经过接近一周的下班后的摸索，总算是成功的利用docker完成了一个django项目的完整部署。不得不说时间已经来到9102年，不会一点docker的话，会给自己带来太多的局限。

相比于传统的部署方式，docker极大地简化了我们的工作。目前我所在的公司内，身为项目负责人的我，也兼顾了一些运维部署的工作。最近项目度过了密集的编码开发阶段，在项目部署阶段，安装系统、数据库、python、虚拟环境。。。真的是让我耗费了很多时间，这就是我为什么花了将近一周的业余时间研究docker的原因。

需要注意的是，如果你对django的传统的部署方式还是不太熟悉，甚至还没有完整的时间过几次，那么我建议暂时先在真实的linux系统上完成至少一次部署，这样才能保证迁移到容器部署后你的基础知识是牢固的。

如果对Docker尚不熟悉的朋友，可以先看一下这下面的系列博文

> 2019-06-19 [Docker构建Django部署环境（三）Docker Compose](https://huangyongchi.com/2019/06/19/Docker%20Compose%E7%AE%80%E4%BB%8B/)
>
> 2019-06-06 [Docker构建Django部署环境（二）基本指令集](https://huangyongchi.com/2019/06/06/docker%E6%8C%87%E4%BB%A4/)
>
> 2019-06-01 [Docker构建Django部署环境（一）核心概念](https://huangyongchi.com/2019/06/01/Docker%E4%B9%8B%E8%B7%AF%EF%BC%88%E4%B8%80%EF%BC%89%E4%B8%80%E4%BA%9B%E6%A6%82%E5%BF%B5/)
>
> 2018-12-19 [CentOS下安装Docker](https://huangyongchi.com/2018/12/19/%E4%BD%BF%E7%94%A8%20yum%20%E5%AE%89%E8%A3%85%EF%BC%88CentOS%207%E4%B8%8B%EF%BC%89/)

## 传统Django部署架构

浏览器访问一个页面的程序处理流程：

(1)，浏览器向 Nginx 发起一个请求，如果匹配到 Nginx 的静态 URL，比如 /static 目录下的 js、css、404.html 等文件，那么 Nginx 直接返回文件。其他请求 URL，通过 uwsgi_pass 配置转给 uWSGI 处理。

(2)，uWSGI 解析请求头、请求体，http 协议转为 wsgi协议内容，最后将请求转给 Django。

(3)，Django 通过 URL 匹配，找到指定的 View 函数，访问 MySQL、Redis 等服务，组装一个 Response 返回给 uWSGI。

(4)，uWSGI 根据返回 Response 生成响应头和响应体，uWSGI 协议转为 http 协议内容，最后将请求转给 Nginx。

(5)，浏览器根据 Nginx 返回的内容，渲染在页面，呈现给用户。

需要说明的是，Nginx 作为反向代理，提供了负载均衡的能力，同时，由于 Nginx 出色的静态文件服务能力，提高了系统的并发能力。uWSGI 通过多 work 提高了 Django 的并发能力，充分发挥了多核的优势。

![Zu7OzD.png](https://s2.ax1x.com/2019/06/28/Zu7OzD.png)

## 本项目架构

在简化架构的基础上，我们还是保证了基础的架构完整性，这个小博客项目里暂时没有用到消息队列与缓存，因此我们的演示架构如下

`django+nginx+uwsgi+postgresql`

项目结构：

``````ini
.
├── docker-compose.yml
├── LICENSE
├── nginx
│   ├── Dockerfile
│   ├── my_nginx.conf
│   ├── nginx.conf
│   └── nginx_origin.conf
├── README.md
└── YcBlog
    ├── blog
    ├── cnblog
    ├── Dockerfile
    ├── manage.py
    ├── media
    ├── requirements.txt
    ├── static
    ├── templates
    └── uwsgi.ini
``````

启动命令：

`docker-compose up`

于是整个Django世界开始运行：）

这里依然还有很多需要补充，但是我现在需要睡觉了。。。

