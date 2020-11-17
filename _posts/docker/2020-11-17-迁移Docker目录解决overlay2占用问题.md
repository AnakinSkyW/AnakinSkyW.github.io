---
layout: post
title: 迁移Docker目录解决overlay2占用问题
date: 2020-11-17 17:30:58
description: 迁移Docker目录解决overlay2占用问题
share: true
tags:
 - docker
comments: true
toc: false
---

## 查看存储情况  

### `/var/lib/docker/`是Docker的根目录，容器和镜像都存储在这个位置，解决占用空间的问题必须迁移该目录，这里先查看该目录使用情况

```bash
[root@iZuf68uxjd82vso8nsfwc9Z docker]# du -hs /var/lib/docker/
12G     /var/lib/docker/
```

###  使用Docker命令`docker system df`查看Docker磁盘使用情况，使用`docker system prune`清理关闭的容器、无用是数据卷和网络，以及无tag的镜像  

```bash
[root@iZuf68uxjd82vso8nsfwc9Z docker]# docker system df
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              17                  4                   5.567GB             4.054GB (72%)
Containers          4                   4                   6.676GB             0B (0%)
Local Volumes       4                   0                   0B                  0B
Build Cache         0                   0                   0B                  0B
```

```bash
[root@iZuf68uxjd82vso8nsfwc9Z docker]# docker system prune
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N] y
Total reclaimed space: 0B
```  

`docker system prune -a` 命令更彻底，会删除没有容器使用的镜像  

### 迁移`/var/lib/docker/`目录

### 先停止docker服务  

`systemctl stop docker`

### 先创建好将要迁移的目录  

`mkdir -p /data/docker/lib`  

### 将`var/lib/docker`同步到`/data/docker/lib`  

`rsync -avz /var/lib/docker /data/docker/lib`  

### 配置`/etc/systemd/system/docker.service.d/devicemapper.conf`。查看`devicemapper.conf`是否存在，如果不存在请创建  

`mkdir -p /etc/systemd/system/docker.service.d/`
`vi /etc/systemd/system/docker.service.d/devicemapper.conf`

### 写入映射目录  

```bash
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd --graph=/data/docker/lib
```

### 重新加载Docker 

`systemctl daemon-reload`
`systemctl restart docker`
`systemctl enable docker`  

### 检查目前Docker根目录，确定当前目录已迁移，再查看容器镜像是否存在`docker images`

```bash
[root@iZuf68uxjd82vso8nsfwc9Z docker]# docker info
...
 Docker Root Dir: /data/docker/lib
... 
```

### 确认无误后删除 `/var/lib/docker`

`rm -rf /var/lib/docker`