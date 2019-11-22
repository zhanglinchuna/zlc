Dockerfile构建镜像是以基础镜像为基础的，Dockerfile是一个文本文件，内容是用户编写的一些docker指令，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。

Dockerfile的基本指令有十三个，分别是：FROM、MAINTAINER、RUN、CMD、EXPOSE、ENV、ADD、COPY、ENTRYPOINT、VOLUME、USER、WORKDIR、ONBUILD

### 一、Dockerfile常用指令

<table>
    <tr>
        <th>类型</th>
        <th>命令</th>
    </tr>
    <tr>
        <td>基础镜像信息</td>
        <td>FROM</td>
    </tr>
    <tr>
        <td>维护者信息</td>
        <td>MAINTAINER</td>
    </tr>
    <tr>
        <td>镜像操作指令</td>
        <td>RUN、COPY、ADD、EXPOSE、WORKDIR、ONBUILD、USER、VOLUME等</td>
    </tr>
    <tr>
        <td>容器启动时执行指令</td>
        <td>CMD、ENTRYPOINT</td>
    </tr>
</table>

#### 1.1 FROM ：指定基础镜像

所谓定制镜像，那一定是以一个镜像为基础，在其上进行定制。就像我们之前运行了一个nginx镜像的容器，再进行修改一样，基础镜像是必须指定的。而FROM就是指定基础镜 像，因此一个Dockerfile中FROM是必备的指令，并且必须是第一条指令。

如：指定ubuntu的14版本作为基础镜像

```
FROM ubuntu:14
```

#### 1.2 RUN：执行命令

RUN指令在新镜像内部执行的命令，如：执行某些动作、安装系统软件、配置系统信息之类，

格式如下两种：

- 1）shell格式：RUN< command > ，就像直接在命令行中输入的命令一样。

如在nginx里的默认主页中写”hello“：

```
RUN echo 'hello ' >/etc/nginx/html/index.html
```

- 2）exec格式：RUN ["可执行文件", "参数1", "参数2"]

如在新镜像中用yum方式安装nginx：

```
RUN ["yum","install","nginx"]
```

> 注：多行命令不要写多个RUN，原因是Dockerfile中每一个指令都会建立一层.多少个RUN就构建了多少层镜像，会造成镜像的臃肿、多层，不仅仅增加了构件部署的时间，还容易出错,RUN书写时的换行符是\

#### 1.3 COPY：复制文件

COPY命令用于将宿主机器上的的文件复制到镜像内，如果目的位置不存在，Docker会自动创建。但宿主机器用要复制的目录必须是和Dockerfile文件统计目录下。

格式：

```
COPY [--chown=<user>:<group>] <源路径>... <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]
```

如把宿主机中的package.json文件复制到容器中/usr/src/app/目录下：

```
COPY package.json /usr/src/app/
```

#### 1.4 CMD：容器启动命令

CMD命令用于容器启动时需要执行的命令，CMD在Dockerfile中只能出现一次，如果出现多个，那么只有最后一个会有效。
其作用是在启动容器的时候提供一个默认的命令项。如果用户执行docker run的时候提供了命令项，就会覆盖掉这个命令，没提供就会使用构建时的命令。

格式：

```
shell 格式：CMD <命令>
exec 格式：CMD ["可执行文件", "参数1", "参数2"...]
```

如容器启动时进入bash：

```
CMD /bin/bash
```

也可以用exec写法：

```
CMD ["/bin/bash"]
```

#### 1.5 MAINTAINER：指定作者

用来指定dockerfile的作者名称和邮箱，主要作用是为了标识软件的所有者是谁。

语法：

```
MAINTAINER <name> <email>
```

如：

```
MAINTAINER autor_zhanglinchun 2601165134@qq.com
```

#### 1.6 EXPOSE：暴露端口

EXPOSE命名适用于设置容器对外映射的容器端口号，如tomcat容器内使用的端口8081，则用EXPOSE命令可以告诉外界该容器的8081端口对外，在构建镜像时用docker run -p可以设置暴露的端口对宿主机器端口的映射。

语法：

```
EXPOSE <端口1> [<端口2>...]
```

如：

```
EXPOSE 8081
```

EXPOSE 8081 其实等价于 docker run -p 8081 当需要把8081端口映射到宿主机中的某个端口（如8888）以便外界访问时，则可以用docker run -p 8888:8081

#### 1.7 WORKDIR：配置工作目录

WORKDIR命令是为RUN、CMD、ENTRYPOINT指令配置工作目录。其效果类似于Linux命名中的cd命令，用于目录的切换，但是和cd不一样的是：如果切换到的目录不存在，WORKDIR会为此创建目录。

语法:

```
WORKDIR path
```

如需要在nginx目录下创建一个hello.txt的文件：

```
##进入/usr/local/nginx目录下
WORKDIR /usr/local/nginx

##进入/usr/local/nginx中的html目录下
WORKDIR html

## 在html目录下创建了一个hello.txt文件
RUN echo 'hello' > hello.txt
```

#### 1.8 ENTRYPOINT：容器启动执行命名

ENTRYPOINT的作用和用法和CMD一模一样，但是ENTRYPOINT有和CMD有2处不一样：

1. CMD的命令会被docker run的命令覆盖而ENTRYPOINT不会
2. CMD和ENTRYPOINT都存在时，CMD的指令变成了ENTRYPOINT的参数，并且此CMD提供的参数会被 docker run 后面的命令覆盖

#### 1.9 VOLUME

VOLUME用来创建一个可以从本地主机或其他容器挂载的挂载点。例如我们知道tomcat的webapps目录是放web应用程序代码的地方，此时我们要把webapps目录挂载为匿名卷，这样任何写入webapps中的心都不会被记录到容器的存储层，让容器存储层无状态化。

格式：

```
VOLUME ["path"]
```

如创建tomcat的webapps目录的一个挂载点

```
VOLUME /usr/local/tomcat/webapps
```

这样，在运行容器时，也可以用过docker run -v来把匿名挂载点挂载都宿主机器上的某个目录，如

```
docker run -d -v /home/tomcat_webapps:/usr/local/tomcat/webapps
```

#### 1.10 USER

USER命令用于指定当前望下执行的用户，需要注意的是这个用户必须是已经存在，否则无法指定。它的用法和WORKDIR有点像，切换用户。

格式：

```
USER daemon
```

#### 1.11 ADD

作用和使用方法和COPY一模一样

#### 1.12 ONBUILD

ONBUILD用于配置当前所创建的镜像作为其它新创建镜像的基础镜像时，所执行的操作指令。

意思就是：这个镜像创建后，如果其它镜像以这个镜像为基础，会先执行这个镜像的ONBUILD命令

格式：

```
ONBUILD [INSTRUCTION]
```

#### 1.13 ENV：设置环境变量

ENV命名用于设置容器的环境变量，这些变量以”key=value”的形式存在，在容器内被脚本或者程序调用，容器运行的时候这个变量也会保留。

格式：

- 1） 设置一个： ENV <key> <value>
- 2） 设置多个：ENV <key1>=<value1> <key2>=<value2>...

如设置一个环境变量JAVA_HOME，接下来的命名就可以使用这个变量：

```
ENV JAVA_HOME /opt/jdk
ENV PATH $PATH:$JAVA_HOME/bin
```

在使用ENV设置环境变量时，有几点需要注意：

- 1）具有传递性，也就是当前镜像被用作其它镜像的基础镜像时，新镜像会拥有当前这个基础镜像所有的环境变量
- 2）ENV定义的环境变量，可以在dockerfile被后面的所有指令（CMD除外）中使用，但不能被docker run 的命令参数引用

如：

```
ENV tomcat_home_name tomcat_7
RUN mkdir $tomcat_home_name
```

- 3）除了ENV之外，docker run -e 也可以设置环境变量传入容器内

如：

```
docker run -d tomcat -e "tomcat_home_name=tomcat_7"
```

这样我们进入容器内部用ENV可以看到tomcat_home_name这个环境变量

### 二、Dockerfile的编写

我们先看一个例子

```
#在centos上安装nginx
FROM centos
#标明著作人的名称和邮箱
MAINTAINER jiabuli 649917837@qq.com
#测试一下网络环境
RUN ping -c 1 www.baidu.com
#安装nginx必要的一些软件
RUN yum -y install gcc make pcre-devel zlib-devel tar zlib
#把nginx安装包复制到/usr/src/目录下
ADD nginx-1.15.8.tar.gz /usr/src/
#切换到/usr/src/nginx-1.15.8编译并且安装nginx
RUN cd /usr/src/nginx-1.15.8 \
    && mkdir /usr/local/nginx \
    && ./configure --prefix=/usr/local/nginx && make && make install \
    && ln -s /usr/local/nginx/sbin/nginx /usr/local/sbin/ \
    && nginx
#删除安装nginx安装目录
RUN rm -rf /usr/src/nginx-nginx-1.15.8
#对外暴露80端口
EXPOSE 80
#启动nginx
CMD ["nginx", "-g", "daemon off;"]
```

上面的例子就是类似于在centos系统上安装一个nginx的的一个过程，因此编写Dockerfile构建镜像就和在Linux上安装软件的流程几乎是一模一样的。所以我们在编写Dockerfile来构建镜像时，可以先思考在Linux上安装该软件的流程，再用Dockerfile提供的指令转化到Dockerfile中即可。

### 三、用Dockerfile构建镜像

用Dockerfile的核心在于编写Dockerfile，但是编写完之后我们需要知道怎么使用Dockerfile来构建镜像，下面以构建nginx镜像为例来简要说明构建流程

#### 3.1 上传安装包

首先我们需要把要构建的软件安装包上传到服务器中，我们可以在服务器目录上创建一个专门的文件夹，如：/var/nginx_build,然后把从nginx官网下载的nginx-1.15.8.tar.gz安装包上传到这个目录里。

#### 3.2 编写Dockerfile

如何编写nginx的Dockerfile上面已经详细介绍，现在我们只需把编写好的Dockerfile上传到/var/nginx_build目录下，当然你也可以在服务器上直接编写Dockerfile，但是要记得一定保证Dockerfile文件和安装包在一个目录下。

#### 3.3 运行构建命令构建

docker build 命令用于使用 Dockerfile 创建镜像。

格式：

```
docker build [OPTIONS] PATH | URL | -
```

OPTIONS有很多指令，下面列举几个常用的：

- --build-arg=[] :设置镜像创建时的变量；
- -f :指定要使用的Dockerfile路径；
- --force-rm :设置镜像过程中删除中间容器；
- --rm :设置镜像成功后删除中间容器；
- --tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；

因此我们构建nginx可以用以下命令：

```
docker build -t nginx:v1.0 .
```

当Dockerfile和当前执行命令的目录不在同一个时，我们也可以指定Dockerfile，如

```
docker build -f /var/nginx_build/Dockerfile .
```

执行命名之后，会看到控制台逐层输出构建内容，直到输出两个Successfully即为构建成功。
