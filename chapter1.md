# 什么是nginx？

Nginx（发音为“ engine-x”）是用于HTTP，HTTPS，SMTP，POP3和IMAP协议的开源反向代理服务器，以及负载平衡器，HTTP缓存和Web服务器（原始服务器）。Nginx项目一开始就非常关注高并发，高性能和低内存使用。它获得了2条款式BSD许可，并在Linux，BSD变体，Mac OS X，Solaris，AIX，HP-UX以及其他\* nix版本上运行。它还具有用于Microsoft Windows的概念证明端口。

> [wikipedia.org/wiki/Nginx](https://en.wikipedia.org/wiki/Nginx)

![](https://raw.githubusercontent.com/docker-library/docs/01c12653951b2fe592c1f93a13b4e289ada0e3a1/nginx/logo.png "商标")

# 如何使用这张图片

## 托管一些简单的静态内容

```
$ docker run --name some-nginx -v /some/content:/usr/share
ginx/html:ro -d nginx
```

或者，`Dockerfile`可以使用简单的方法来生成包含必要内容的新图像（这比上面的绑定安装要干净得多）：

```
FROM nginx

COPY static-html-directory /usr/share/nginx/html
```

将此文件放置在与目录相同的目录中（“ static-html-directory”），运行`docker build -t some-content-nginx .`，然后启动容器：

```
$ docker run --name some-nginx -d some-content-nginx
```

## 暴露外部端口

```
$ docker run --name some-nginx -d -p 8080:80 some-content-nginx
```

然后，您可以在浏览器中点击`http://localhost:8080`或`http://host-ip:8080`。

## 复杂的配置

```
$ docker run --name my-custom-nginx-container -v /host/path
ginx.conf:/etc
ginx
ginx.conf:ro -d nginx
```

有关nginx配置文件的语法的信息，请参阅[官方文档](http://nginx.org/en/docs/)（特别是《[入门指南》](http://nginx.org/en/docs/beginners_guide.html#conf_structure)）。

如果您希望采用默认配置，请使用以下类似内容从运行的nginx容器中复制它：

```
$ docker run --name tmp-nginx-container -d nginx

$ docker cp tmp-nginx-container:/etc/nginx/nginx.conf /host/path/nginx.conf

$ docker rm -f tmp-nginx-container
```

也可以使用简单的`Dockerfile`（在中`/host/path/`）更干净地完成此操作：

```
FROM nginx

COPY nginx.conf /etc/nginx/nginx.conf
```

如果您`CMD`在Dockerfile中添加了自定义，请确保将包含`-g daemon off;`在其中`CMD`，以使nginx保持在前台，以便Docker可以正确跟踪进程（否则您的容器将在启动后立即停止）！

然后使用构建图像`docker build -t custom-nginx .`并按如下所示运行它：

```
$ docker run --name my-custom-nginx-container -d custom-nginx
```

### 在Nginx配置中使用环境变量

现成的nginx不支持大多数配置块中的环境变量。但是，`envsubst`如果您需要在nginx启动之前动态生成nginx配置，则可以将其用作解决方法。

这是使用docker-compose.yml的示例：

```
web:

  image: nginx

  volumes:

   - ./mysite.template:/etc/nginx/conf.d/mysite.template

  ports:

   - "8080:80"

  environment:

   - NGINX_HOST=foobar.com

   - NGINX_PORT=80

  command: /bin/bash -c "envsubst 
<
 /etc
ginx/conf.d/mysite.template 
>
 /etc
ginx/conf.d/default.conf 
&
&
 exec nginx -g 'daemon off;'"
```

`mysite.template`然后，该文件可能包含如下变量引用：

`listen ${NGINX_PORT};`

## 在只读模式下运行Nginx

要以只读模式运行nginx，您需要将Docker卷安装到nginx写入信息的每个位置。默认的nginx配置需要对`/var/cache`和的写权限`/var/run`。可以通过如下运行nginx轻松地完成此操作：

```
$ docker run -d -p 80:80 --read-only -v $(pwd)
ginx-cache:/var/cache
ginx -v $(pwd)
ginx-pid:/var/run nginx
```

如果您具有更高级的配置，需要nginx写入其他位置，只需将更多的卷安装添加到这些位置。

## 在调试模式下运行Nginx

自1.9.8版起的图像带有`nginx-debug`二进制文件，当使用较高的日志级别时，该文件会产生详细的输出。它可以与简单的CMD替换一起使用：

```
$ docker run --name my-nginx -v /host/path
ginx.conf:/etc
ginx
ginx.conf:ro -d nginx nginx-debug -g 'daemon off;'
```

docker-compose.yml中的类似配置如下所示：

```
web:

  image: nginx

  volumes:

    - ./nginx.conf:/etc/nginx/nginx.conf:ro

  command: [nginx-debug, '-g', 'daemon off;']
```

## 用户和组ID

从1.17.0开始，基于高山和基于debian的映像变体都使用相同的用户名和组ID来放弃工作进程的特权：

```
$ id

uid=101(nginx) gid=101(nginx) groups=101(nginx)
```

## 以非root用户身份运行nginx

可以将映像作为特权较低的任意UID / GID运行。但是，这需要修改nginx配置，以使用该特定UID / GID对可写的目录：

```
$ docker run -d -v $PWD
ginx.conf:/etc
ginx
ginx.conf nginx
```

当前目录中的nginx.conf应该重新定义以下指令：

```
pid        /tmp
ginx.pid;
```

并在http上下文中：

```
http {

    client_body_temp_path /tmp/client_temp;

    proxy_temp_path       /tmp/proxy_temp_path;

    fastcgi_temp_path     /tmp/fastcgi_temp;

    uwsgi_temp_path       /tmp/uwsgi_temp;

    scgi_temp_path        /tmp/scgi_temp;

...

}
```

## 使用Amplify监控Nginx

[Amplify](https://amplify.nginx.com/signup/)是一个免费的监视工具，可用于监视基于nginx的微服务架构。Amplify由Nginx软件背后的公司开发和维护。

借助Amplify，可以跨容器收集和汇总指标，并提供关键绩效数据（如活动连接或每秒请求）的连贯可视化集。快速检查是否有性能下降，流量异常，并更全面地了解Nginx配置也很容易。

为了使用Amplify，应在容器内部[安装](https://github.com/nginxinc/docker-nginx-amplify)基于Python的小型代理软件（Amplify Agent）。

有关Amplify的更多信息，请在[此处](https://github.com/nginxinc/nginx-amplify-doc)查看官方文档。

# 图像变体什么是nginx？

Nginx（发音为“ engine-x”）是用于HTTP，HTTPS，SMTP，POP3和IMAP协议的开源反向代理服务器，以及负载平衡器，HTTP缓存和Web服务器（原始服务器）。Nginx项目一开始就非常关注高并发，高性能和低内存使用。它获得了2条款式BSD许可，并在Linux，BSD变体，Mac OS X，Solaris，AIX，HP-UX以及其他\* nix版本上运行。它还具有用于Microsoft Windows的概念证明端口。

> [wikipedia.org/wiki/Nginx](https://en.wikipedia.org/wiki/Nginx)

![](https://raw.githubusercontent.com/docker-library/docs/01c12653951b2fe592c1f93a13b4e289ada0e3a1/nginx/logo.png "商标")

# 如何使用这张图片

## 托管一些简单的静态内容

```
$ docker run --name some-nginx -v /some/content:/usr/share
ginx/html:ro -d nginx
```

或者，`Dockerfile`可以使用简单的方法来生成包含必要内容的新图像（这比上面的绑定安装要干净得多）：

```
FROM nginx

COPY static-html-directory /usr/share/nginx/html
```

将此文件放置在与目录相同的目录中（“ static-html-directory”），运行`docker build -t some-content-nginx .`，然后启动容器：

```
$ docker run --name some-nginx -d some-content-nginx
```

## 暴露外部端口

```
$ docker run --name some-nginx -d -p 8080:80 some-content-nginx
```

然后，您可以在浏览器中点击`http://localhost:8080`或`http://host-ip:8080`。

## 复杂的配置

```
$ docker run --name my-custom-nginx-container -v /host/path
ginx.conf:/etc
ginx
ginx.conf:ro -d nginx
```

有关nginx配置文件的语法的信息，请参阅[官方文档](http://nginx.org/en/docs/)（特别是《[入门指南》](http://nginx.org/en/docs/beginners_guide.html#conf_structure)）。

如果您希望采用默认配置，请使用以下类似内容从运行的nginx容器中复制它：

```
$ docker run --name tmp-nginx-container -d nginx

$ docker cp tmp-nginx-container:/etc/nginx/nginx.conf /host/path/nginx.conf

$ docker rm -f tmp-nginx-container
```

也可以使用简单的`Dockerfile`（在中`/host/path/`）更干净地完成此操作：

```
FROM nginx

COPY nginx.conf /etc/nginx/nginx.conf
```

如果您`CMD`在Dockerfile中添加了自定义，请确保将包含`-g daemon off;`在其中`CMD`，以使nginx保持在前台，以便Docker可以正确跟踪进程（否则您的容器将在启动后立即停止）！

然后使用构建图像`docker build -t custom-nginx .`并按如下所示运行它：

```
$ docker run --name my-custom-nginx-container -d custom-nginx
```

### 在Nginx配置中使用环境变量

现成的nginx不支持大多数配置块中的环境变量。但是，`envsubst`如果您需要在nginx启动之前动态生成nginx配置，则可以将其用作解决方法。

这是使用docker-compose.yml的示例：

```
web:

  image: nginx

  volumes:

   - ./mysite.template:/etc/nginx/conf.d/mysite.template

  ports:

   - "8080:80"

  environment:

   - NGINX_HOST=foobar.com

   - NGINX_PORT=80

  command: /bin/bash -c "envsubst 
<
 /etc
ginx/conf.d/mysite.template 
>
 /etc
ginx/conf.d/default.conf 
&
&
 exec nginx -g 'daemon off;'"
```

`mysite.template`然后，该文件可能包含如下变量引用：

`listen ${NGINX_PORT};`

## 在只读模式下运行Nginx

要以只读模式运行nginx，您需要将Docker卷安装到nginx写入信息的每个位置。默认的nginx配置需要对`/var/cache`和的写权限`/var/run`。可以通过如下运行nginx轻松地完成此操作：

```
$ docker run -d -p 80:80 --read-only -v $(pwd)
ginx-cache:/var/cache
ginx -v $(pwd)
ginx-pid:/var/run nginx
```

如果您具有更高级的配置，需要nginx写入其他位置，只需将更多的卷安装添加到这些位置。

## 在调试模式下运行Nginx

自1.9.8版起的图像带有`nginx-debug`二进制文件，当使用较高的日志级别时，该文件会产生详细的输出。它可以与简单的CMD替换一起使用：

```
$ docker run --name my-nginx -v /host/path
ginx.conf:/etc
ginx
ginx.conf:ro -d nginx nginx-debug -g 'daemon off;'
```

docker-compose.yml中的类似配置如下所示：

```
web:

  image: nginx

  volumes:

    - ./nginx.conf:/etc/nginx/nginx.conf:ro

  command: [nginx-debug, '-g', 'daemon off;']
```

## 用户和组ID

从1.17.0开始，基于高山和基于debian的映像变体都使用相同的用户名和组ID来放弃工作进程的特权：

```
$ id

uid=101(nginx) gid=101(nginx) groups=101(nginx)
```

## 以非root用户身份运行nginx

可以将映像作为特权较低的任意UID / GID运行。但是，这需要修改nginx配置，以使用该特定UID / GID对可写的目录：

```
$ docker run -d -v $PWD
ginx.conf:/etc
ginx
ginx.conf nginx
```

当前目录中的nginx.conf应该重新定义以下指令：

```
pid        /tmp
ginx.pid;
```

并在http上下文中：

```
http {

    client_body_temp_path /tmp/client_temp;

    proxy_temp_path       /tmp/proxy_temp_path;

    fastcgi_temp_path     /tmp/fastcgi_temp;

    uwsgi_temp_path       /tmp/uwsgi_temp;

    scgi_temp_path        /tmp/scgi_temp;

...

}
```

## 使用Amplify监控Nginx

[Amplify](https://amplify.nginx.com/signup/)是一个免费的监视工具，可用于监视基于nginx的微服务架构。Amplify由Nginx软件背后的公司开发和维护。

借助Amplify，可以跨容器收集和汇总指标，并提供关键绩效数据（如活动连接或每秒请求）的连贯可视化集。快速检查是否有性能下降，流量异常，并更全面地了解Nginx配置也很容易。

为了使用Amplify，应在容器内部[安装](https://github.com/nginxinc/docker-nginx-amplify)基于Python的小型代理软件（Amplify Agent）。

有关Amplify的更多信息，请在[此处](https://github.com/nginxinc/nginx-amplify-doc)查看官方文档。

# 图像变体

该`nginx`图像有许多种，每一个设计用于特定的使用情况。

## `nginx:<version>`

这是实际图像。如果不确定自己的需求，则可能要使用这一需求。它既可以用作一次性容器（装入源代码并启动容器以启动应用程序），也可以用作构建其他图像的基础。

## `nginx:<version>-alpine`

该图像是基于流行的[高山Linux项目](http://alpinelinux.org/)，可用[的`alpine`官方图片](https://hub.docker.com/_/alpine)。Alpine Linux比大多数分发基础映像（〜5MB）小得多，因此通常导致更苗条的映像。

当希望最终图像尺寸尽可能小时，强烈建议使用此变体。需要注意的主要警告是，它确实使用了[musl libc](http://www.musl-libc.org/)而不是[glibc和friends](http://www.etalabs.net/compare_libcs.html)，因此某些软件可能会遇到问题，具体取决于其libc要求的深度。但是，大多数软件对此没有问题，因此，此变体通常是非常安全的选择。请参阅[此Hacker News评论主题](https://news.ycombinator.com/item?id=10782897)，以获取有关可能出现的问题的更多讨论以及使用基于Alpine的图像的一些优缺点的比较。

为了最大程度地减小图像大小，通常不会在基于Alpine的图像中包含其他相关工具（例如`git`或`bash`）。以该映像为基础，在自己的Dockerfile中添加所需的内容（如果不熟悉，请参阅[`alpine`映像描述](https://hub.docker.com/_/alpine/)以获取有关如何安装软件包的示例）。

该`nginx`图像有许多种，每一个设计用于特定的使用情况。

## `nginx:<version>`

这是实际图像。如果不确定自己的需求，则可能要使用这一需求。它既可以用作一次性容器（装入源代码并启动容器以启动应用程序），也可以用作构建其他图像的基础。

## `nginx:<version>-alpine`

该图像是基于流行的[高山Linux项目](http://alpinelinux.org/)，可用[的`alpine`官方图片](https://hub.docker.com/_/alpine)。Alpine Linux比大多数分发基础映像（〜5MB）小得多，因此通常导致更苗条的映像。

当希望最终图像尺寸尽可能小时，强烈建议使用此变体。需要注意的主要警告是，它确实使用了[musl libc](http://www.musl-libc.org/)而不是[glibc和friends](http://www.etalabs.net/compare_libcs.html)，因此某些软件可能会遇到问题，具体取决于其libc要求的深度。但是，大多数软件对此没有问题，因此，此变体通常是非常安全的选择。请参阅[此Hacker News评论主题](https://news.ycombinator.com/item?id=10782897)，以获取有关可能出现的问题的更多讨论以及使用基于Alpine的图像的一些优缺点的比较。

为了最大程度地减小图像大小，通常不会在基于Alpine的图像中包含其他相关工具（例如`git`或`bash`）。以该映像为基础，在自己的Dockerfile中添加所需的内容（如果不熟悉，请参阅[`alpine`映像描述](https://hub.docker.com/_/alpine/)以获取有关如何安装软件包的示例）。



