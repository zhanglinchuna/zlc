Dockerfile构建镜像是以基础镜像为基础的，Dockerfile是一个文本文件，内容是用户编写的一些docker指令，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。

Dockerfile的基本指令有十三个，分别是：FROM、MAINTAINER、RUN、CMD、EXPOSE、ENV、ADD、COPY、ENTRYPOINT、VOLUME、USER、WORKDIR、ONBUILD

### 一、Dockerfile常用指令

