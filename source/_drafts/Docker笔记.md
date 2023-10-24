---
title: Docker笔记
date: 2023-09-28 16:41:32
tags: [云原生,云计算]
categories: 云计算
---

### 存储卷(Volumes)

 - 匿名卷：可以在Dockerfile中使用`VOLUME [ "/path" ]`创建匿名卷，也可以在启动容器的时候用`-v /path`来创建。匿名卷在container被删掉之后会被自动删除。
 - 命名卷：可以在启动容器的时候用`-v volume_name:/path`来创建命名卷。命名卷在容器被删除时不会被自动删除。
 - 绑定挂载(Bind Mount)：在启动容器时使用`-v host_path:/container_path`将主机上的目录映射到容器中。这样主机上文件的改动就能直接反映在容器里，无需重新构建容器镜像。


 ### 容器网络

 - 容器中可以使用`host.docker.internal`作为主机名来访问主机网络。
 - 多个容器可以通过`--network {network_name}`加入同一网络，同一网络中的容器可以通过容器名互相访问。
 - Docker网络有`bridge, host, none, overlay, macvlan, third-party plugin`等类型，在创建网络是通过`--driver`来指定。