---
title: docker 的安装和配置
categories: ["环境安装", "docker"]
tags: ["环境安装", "docker"]
copyright: true
date: 2020-06-27 22:57:26
---

>  centos7 安装 docker  : [Get Docker Engine - Community for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/) 

<!--more-->

# 1. 环境准备

1. 检查内核版本，如果内核低于 3.10，需要更新系统
```bash
uname -r  # 检查版本
yum update # 更新
```
2. 卸载旧版本 docker
```bash
yum remove docker \
            docker-client \
            docker-client-latest \
            docker-common \
            docker-latest \
            docker-latest-logrotate \
            docker-logrotate \
            docker-engine
```



# 2. 使用存储库安装

## 2.1 设置存储库

1. 安装所需的软件包
```bash
yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

2. 设置稳定的存储库
```bash
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```



## 2.2 安装 DOCKER ENGINE 社区版



### 2.2.1 安装 最新版本

1. 安装最新版本，或者转到下一步安装指定版本
```bash
yum install -y docker-ce docker-ce-cli containerd.io
```
2. 查看 docker 版本，是否安装成功
```bash
docker -v
```



### 2.2.2 安装指定版本

1. 列出存储库中可用的版本
```bash
yum list docker-ce --showduplicates | sort -r
```

2. 安装特定版本，该软件包名称是软件包名称（docker-ce）加上版本字符串（第二列），从第一个冒号（:）一直到第一个连字符，并用连字符（-）分隔。例如，docker-ce-18.09.1。
```bash
yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```



### 2.2.3 启动 docker

1. 启动 dcoker
```bash
systemctl start docker
```
2. 设置为开机启动
```bash
systemctl enable docker
```



# 3. 配置镜像加速器

> 参考 [Docker 配置加速器](https://cloud.tencent.com/developer/article/1151242)

## 3.1 国内加速地址

1. Docker 中国区官方镜像，速度不是很好
```
https://registry.docker-cn.com
```

2. 网易
```
http://hub-mirror.c.163.com
```

3. 腾讯云
```
https://mirror.ccs.tencentyun.com
```

4. 中国科技大学
```
https://docker.mirrors.ustc.edu.cn
```

5. **阿里云容器**，免费配置专属于自己的加速器， 控制台->产品与服务->镜像加速器

```
# 这个是我的加速器
https://uksg0tr3.mirror.aliyuncs.com
```



## 3.2 配置加速器

1. 查看是否配置了加速器
```
docker info
```

2. 创建或修改 /etc/docker/daemon.json
```
vim /etc/docker/daemon.json
```

2. 修改 daemon.json 文件，加入下面内容
可以将下面的加速地址换成自己的
```
{
    "registry-mirrors": ["https://uksg0tr3.mirror.aliyuncs.com"]
}
```

3. 执行下面命令使其生效
```
systemctl daemon-reload
systemctl restart docker
```



# 4. 卸载 docker

## 4.1 第一种方法

```bash
yum remove docker \
            docker-client \
            docker-client-latest \
            docker-common \
            docker-latest \
            docker-latest-logrotate \
            docker-logrotate \
            docker-engine
```
## 4.2 第二种

1. 先查看当前安装过的 docker 版本

```bash
yum list installed | grep docker
```

```bash
containerd.io.x86_64                 1.2.13-3.1.el7                  @docker-ce-stable
docker-ce.x86_64                     3:19.03.8-3.el7                 @docker-ce-stable
docker-ce-cli.x86_64                 1:19.03.8-3.el7                 @docker-ce-stable
```

2. 删除安装的软件包
```bash
yum -y remove containerd.io.x86_64 
yum -y remove docker-ce.x86_64
yum -y remove docker-ce-cli.x86_64 
```


3. 主机上的映像，容器，卷或自定义配置文件不会自动删除。要删除所有图像，容器和卷：
```bash
rm -rf /var/lib/docker
```