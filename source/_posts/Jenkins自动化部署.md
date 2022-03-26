---
title: Jenkins自动化部署
date: 2022-02-11 14:06:21
index_img: https://s2.loli.net/2022/03/26/RXEB6bHe5yhQpdZ.png
banner_img: https://s2.loli.net/2022/03/26/KmNkZBTlMvVhQDj.png
tags:
- deploy
categories:
- 后端
---

# Jenkins配置自动化部署

> 本文章系统环境为ubuntu(Debian通用)

## 前置依赖

- [jdk](https://zhuanlan.zhihu.com/p/137114682)

  *安装好之后被安装在`usr/lib/jvm`下*

- [maven](https://cloud.tencent.com/developer/article/1649751)

  *建议手动下载上传安装，建议将包放在`/opt/`下(别忘了配置环境变量)*

- git

  *不会吧，不会吧，你的ubuntu没有git?*

## 安装jenkins

> 看[这篇文章](https://cloud.tencent.com/developer/article/1666282)

重启jenkins：

```sh
systemctl restart jenkins
```

## jenkins部署环境配置

1. 点击<u>Manage Jenkins</u>

2. 点击<u>Global Tool Configuration</u>

3. 配置jdk和jdk的路径

4. 配置git和git的路径

5. 配置maven和maven的路径

## 配置一个项目，开始持续集成

1. 点击<u>新建Item</u>, 点击创建一个FreeStyle的项目

2. 写项目描述

3. 填写git地址, 添加用户认证

4. 创建构建脚本(<u>excute shell</u>)

  ```sh
  #!/bin/bash
  # maven打包
  cd ./blog-api
  mvn clean package
  echo 'package ok!'
  echo 'build start!'
  service_name="blog_api"
  service_port=8888
  # 查看镜像id
  IID=$(docker images | grep "$service_name" | awk '{print $3}')
  echo "IID $IID"
  if [ -n "$IID" ]; then
      echo "exist $SERVER_NAME image, IID=$IID"
      # 删除镜像
      docker rmi -f $service_name
      echo "delete $SERVER_NAME image"
      # 构建
      docker build -t $service_name .
      echo "build $SERVER_NAME image"
  else
      echo "no exist $SERVER_NAME image, build docker"
      # 构建
      docker build -t $service_name .
      echo "build $SERVER_NAME image"
  fi
  # 查看容器id
  CID=$(docker ps | grep "$SERVER_NAME" | awk '{print $1}')
  echo "CID $CID"
  if [ -n "$CID" ]; then
      echo "exist $SERVER_NAME container, CID=$CID"
      # 停止
      docker stop $service_name
      # 删除容器
      docker rm $service_name
  else
      echo "no exist $SERVER_NAME container"
  fi
  # 启动
  docker run -d --name $service_name --net=host -p $service_port:$service_port $service_name
  # 查看启动日志
  docker logs -f $service_name
  ```

  这个脚本可能需要稍加更改

## 添加触发器

当然，我们用jenkins自动部署怎么少得了触发器呢？

[看这里](http://callmedadaxin.github.io/2018/07/20/jenkins-github-hook/)

**之后，我们只需要在本地提交代码，就能自动部署到服务器上**

## 杂项

[设置国内源](https://www.jianshu.com/p/2389ed2d2cb0)

