---
title: Docker 常用命令速查手册
date: 2026-04-22 22:41:32
tags: [docker]
categories: 基础
---


# Docker 常用命令速查手册

> 快速查找 Docker 常用命令，提高工作效率

---

## 目录

- [镜像管理](#镜像管理)
- [容器管理](#容器管理)
- [容器操作](#容器操作)
- [网络管理](#网络管理)
- [数据卷管理](#数据卷管理)
- [日志与监控](#日志与监控)
- [系统信息](#系统信息)
- [清理命令](#清理命令)
- [实用技巧](#实用技巧)

---

## 镜像管理

### 搜索镜像

```bash
# 搜索镜像
docker search <镜像名>

# 示例：搜索 nginx
docker search nginx

# 只显示官方镜像
docker search --filter is-official=true nginx

# 限制搜索结果数量
docker search --limit 5 nginx
```

### 拉取镜像

```bash
# 拉取最新版本
docker pull <镜像名>

# 拉取指定版本
docker pull <镜像名>:<标签>

# 示例
docker pull nginx
docker pull nginx:1.21
docker pull nginx:alpine
docker pull nginx@sha256:abc123...
```

### 查看镜像

```bash
# 列出所有镜像
docker images

# 列出所有镜像（包含中间层）
docker images -a

# 只显示镜像ID
docker images -q

# 格式化输出
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# 查看镜像详细信息
docker inspect <镜像名或ID>

# 查看镜像历史
docker history <镜像名>
```

### 构建镜像

```bash
# 使用 Dockerfile 构建镜像
docker build -t <镜像名>:<标签> <路径>

# 示例
docker build -t myapp:1.0 .
docker build -f Dockerfile.prod -t myapp:prod .

# 构建时不使用缓存
docker build --no-cache -t myapp:1.0 .

# 构建时传递构建参数
docker build --build-arg APP_VERSION=1.0 -t myapp .
```

### 删除镜像

```bash
# 删除单个镜像
docker rmi <镜像名或ID>

# 强制删除（即使有容器在使用）
docker rmi -f <镜像名或ID>

# 删除多个镜像
docker rmi <镜像1> <镜像2> <镜像3>

# 删除所有未使用的镜像
docker image prune

# 删除所有未使用的镜像（包括未标记的）
docker image prune -a

# 删除所有镜像（危险）
docker rmi $(docker images -q)
```

### 导出/导入镜像

```bash
# 导出镜像到 tar 文件
docker save -o <文件名>.tar <镜像名>

# 示例
docker save -o nginx.tar nginx:latest

# 导入镜像
docker load -i <文件名>.tar

# 示例
docker load -i nginx.tar

# 导出镜像到标准输出
docker save <镜像名> > <文件名>.tar

# 从标准输出导入
docker load < <文件名>.tar
```

### 标记镜像

```bash
# 为镜像添加新标签
docker tag <源镜像> <新镜像名>:<标签>

# 示例
docker tag nginx:latest my-nginx:1.0
docker tag nginx:latest registry.example.com/my-nginx:1.0
```

---

## 容器管理

### 创建容器

```bash
# 创建容器（不启动）
docker create [选项] <镜像名>

# 常用选项
docker create \
  --name my-container \
  -p 8080:80 \
  -v /host/path:/container/path \
  -e KEY=VALUE \
  -d \
  <镜像名>
```

### 启动容器

```bash
# 创建并启动容器
docker run [选项] <镜像名>

# 示例：后台运行
docker run -d --name my-nginx nginx

# 示例：交互式运行
docker run -it --name my-ubuntu ubuntu /bin/bash

# 示例：端口映射
docker run -d -p 8080:80 nginx

# 示例：挂载卷
docker run -d -v /data:/app/data nginx

# 示例：环境变量
docker run -d -e MYSQL_ROOT_PASSWORD=123456 mysql

# 示例：自动删除容器（退出后）
docker run --rm -it ubuntu /bin/bash

# 示例：重启策略
docker run -d --restart=always nginx
docker run -d --restart=on-failure:5 nginx
docker run -d --restart=unless-stopped nginx
```

### 查看容器

```bash
# 查看运行中的容器
docker ps

# 查看所有容器（包括已停止）
docker ps -a

# 只显示容器ID
docker ps -q

# 显示容器大小
docker ps -s

# 显示最新创建的容器
docker ps -l

# 格式化输出
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# 查看容器详细信息
docker inspect <容器名或ID>

# 查看容器进程
docker top <容器名或ID>

# 查看容器端口映射
docker port <容器名或ID>
```

### 启动/停止容器

```bash
# 启动已停止的容器
docker start <容器名或ID>

# 停止运行中的容器
docker stop <容器名或ID>

# 重启容器
docker restart <容器名或ID>

# 强制停止容器
docker kill <容器名或ID>

# 暂停容器
docker pause <容器名或ID>

# 恢复暂停的容器
docker unpause <容器名或ID>
```

### 删除容器

```bash
# 删除已停止的容器
docker rm <容器名或ID>

# 强制删除运行中的容器
docker rm -f <容器名或ID>

# 删除多个容器
docker rm <容器1> <容器2> <容器3>

# 删除所有已停止的容器
docker container prune

# 删除所有容器（危险）
docker rm -f $(docker ps -aq)

# 删除所有已停止的容器
docker rm $(docker ps -a -q)
```

### 容器重命名

```bash
# 重命名容器
docker rename <旧名称> <新名称>

# 示例
docker rename my-container new-container
```

---

## 容器操作

### 进入容器

```bash
# 使用 bash 进入（推荐）
docker exec -it <容器名或ID> /bin/bash

# 使用 sh 进入（轻量级容器）
docker exec -it <容器名或ID> /bin/sh

# Linux 系统需要 sudo
sudo docker exec -it <容器名或ID> /bin/bash

# 以 root 用户进入
docker exec -it -u root <容器名或ID> /bin/bash

# 在指定目录下进入
docker exec -it -w /app <容器名或ID> /bin/bash
```

### 在容器中执行命令

```bash
# 执行单条命令
docker exec <容器名或ID> <命令>

# 示例
docker exec my-nginx ls -la
docker exec my-nginx nginx -v
docker exec mysql-db mysql -u root -p -e "SHOW DATABASES;"

# 交互式执行命令
docker exec -it <容器名或ID> <命令>

# 后台执行命令
docker exec -d <容器名或ID> <命令>

# 执行多条命令
docker exec <容器名或ID> /bin/sh -c "cd /tmp && ls -la"

# 传递环境变量
docker exec -e DEBUG=true <容器名或ID> <命令>
```

### 查看日志

```bash
# 查看容器日志
docker logs <容器名或ID>

# 实时跟踪日志
docker logs -f <容器名或ID>

# 显示最后 N 行
docker logs --tail 100 <容器名或ID>

# 显示带时间戳的日志
docker logs -t <容器名或ID>

# 显示最近 N 分钟的日志
docker logs --since 10m <容器名或ID>

# 显示指定时间段的日志
docker logs --since "2024-01-01T00:00:00" --until "2024-01-02T00:00:00" <容器名或ID>

# 组合使用
docker logs -f --tail 50 -t <容器名或ID>
```

### 复制文件

```bash
# 从容器复制到宿主机
docker cp <容器名或ID>:<容器内路径> <宿主机路径>

# 从宿主机复制到容器
docker cp <宿主机路径> <容器名或ID>:<容器内路径>

# 示例
docker cp my-nginx:/etc/nginx/nginx.conf ./nginx.conf
docker cp ./index.html my-nginx:/usr/share/nginx/html/
docker cp my-nginx:/usr/share/nginx/html ./html_backup/

# Linux 系统需要 sudo
sudo docker cp my-nginx:/etc/nginx/nginx.conf ./nginx.conf
```

### 附加到容器

```bash
# 附加到容器主进程
docker attach <容器名或ID>

# 注意：退出时使用 Ctrl+P 然后 Ctrl+Q，否则容器会停止
```

### 导出/导入容器

```bash
# 导出容器为 tar 文件
docker export <容器名或ID> > <文件名>.tar

# 示例
docker export my-container > my-container.tar

# 导入容器为镜像
docker import <文件名>.tar <新镜像名>:<标签>

# 示例
docker import my-container.tar my-image:1.0
```

### 更新容器

```bash
# 更新容器重启策略
docker update --restart=always <容器名或ID>

# 更新容器资源限制
docker update --memory="512m" --cpus="1.0" <容器名或ID>

# 示例
docker update --restart=unless-stopped my-nginx
docker update --memory="1g" --cpus="2.0" my-app
```

---

## 网络管理

### 查看网络

```bash
# 列出所有网络
docker network ls

# 查看网络详细信息
docker network inspect <网络名>

# 查看容器连接的网络
docker network inspect <网络名> --format '{{range .Containers}}{{.Name}}{{end}}'
```

### 创建网络

```bash
# 创建桥接网络（默认）
docker network create <网络名>

# 创建自定义驱动网络
docker network create -d bridge <网络名>

# 指定子网和网关
docker network create \
  --driver=bridge \
  --subnet=172.20.0.0/16 \
  --gateway=172.20.0.1 \
  my-network

# 示例
docker network create my-app-network
docker network create --driver bridge --subnet=192.168.100.0/24 my-network
```

### 连接容器到网络

```bash
# 将容器连接到网络
docker network connect <网络名> <容器名或ID>

# 指定别名
docker network connect --alias <别名> <网络名> <容器名或ID>

# 示例
docker network connect my-network my-nginx
docker network connect --alias web my-network my-nginx
```

### 断开容器网络

```bash
# 断开容器与网络的连接
docker network disconnect <网络名> <容器名或ID>

# 强制断开
docker network disconnect -f <网络名> <容器名或ID>

# 示例
docker network disconnect my-network my-nginx
```

### 删除网络

```bash
# 删除网络
docker network rm <网络名>

# 删除多个网络
docker network rm <网络1> <网络2>

# 删除所有未使用的网络
docker network prune

# 删除所有网络（危险）
docker network rm $(docker network ls -q)
```

### 网络类型

| 网络类型 | 说明 | 用途 |
| :--- | :--- | :--- |
| **bridge** | 默认网络，容器间可通过IP通信 | 单机多容器通信 |
| **host** | 容器使用宿主机网络栈 | 高性能网络需求 |
| **none** | 容器无网络连接 | 完全隔离环境 |
| **overlay** | 跨主机网络 | Docker Swarm 集群 |
| **macvlan** | 为容器分配MAC地址 | 需要容器像物理设备 |

---

## 数据卷管理

### 查看卷

```bash
# 列出所有卷
docker volume ls

# 查看卷详细信息
docker volume inspect <卷名>

# 查看卷使用情况
docker volume ls -f dangling=true
```

### 创建卷

```bash
# 创建卷
docker volume create <卷名>

# 创建带标签的卷
docker volume create --label <标签>=<值> <卷名>

# 示例
docker volume create my-data
docker volume create --label env=prod my-prod-data
```

### 使用卷

```bash
# 运行容器时使用卷
docker run -d -v <卷名>:<容器内路径> <镜像名>

# 运行容器时使用绑定挂载
docker run -d -v <宿主机路径>:<容器内路径> <镜像名>

# 只读挂载
docker run -d -v <卷名>:<容器内路径>:ro <镜像名>

# 示例
docker run -d -v my-data:/app/data nginx
docker run -d -v /host/data:/container/data nginx
docker run -d -v my-data:/app/data:ro nginx
```

### 删除卷

```bash
# 删除卷
docker volume rm <卷名>

# 删除多个卷
docker volume rm <卷1> <卷2>

# 删除所有未使用的卷
docker volume prune

# 删除所有未使用的卷（包括匿名卷）
docker volume prune -a

# 删除所有卷（危险）
docker volume rm $(docker volume ls -q)
```

### 卷备份与恢复

```bash
# 备份卷到 tar 文件
docker run --rm -v <卷名>:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz /data

# 从 tar 文件恢复卷
docker run --rm -v <卷名>:/data -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /

# 示例
docker run --rm -v my-data:/data -v $(pwd):/backup alpine tar czf /backup/my-data-backup.tar.gz /data
```

---

## 日志与监控

### 查看日志

```bash
# 查看容器日志
docker logs <容器名或ID>

# 实时跟踪日志
docker logs -f <容器名或ID>

# 显示最后 N 行
docker logs --tail 100 <容器名或ID>

# 显示带时间戳的日志
docker logs -t <容器名或ID>

# 显示最近 N 分钟的日志
docker logs --since 10m <容器名或ID>

# 组合使用
docker logs -f --tail 50 -t <容器名或ID>
```

### 查看资源使用

```bash
# 查看容器资源使用情况（实时）
docker stats

# 查看指定容器资源使用
docker stats <容器名或ID>

# 不持续更新（只显示一次）
docker stats --no-stream

# 格式化输出
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"

# 示例
docker stats my-nginx
docker stats --no-stream my-nginx
```

### 查看事件

```bash
# 查看 Docker 事件
docker events

# 查看最近 N 分钟的事件
docker events --since 10m

# 查看指定容器的事件
docker events --filter container=<容器名或ID>

# 格式化输出
docker events --format "{{.Status}} {{.Actor.Attributes.name}}"
```

### 健康检查

```bash
# 查看容器健康状态
docker inspect --format='{{.State.Health.Status}}' <容器名或ID>

# 运行时指定健康检查
docker run --health-cmd="curl -f http://localhost/ || exit 1" \
  --health-interval=5s \
  --health-timeout=3s \
  --health-retries=3 \
  nginx
```

---

## 系统信息

### 查看 Docker 信息

```bash
# 查看 Docker 系统信息
docker info

# 查看 Docker 版本
docker version

# 查看 Docker 详细版本
docker version --format '{{.Server.Version}}'

# 查看客户端和服务端版本
docker version --format 'Client: {{.Client.Version}}\nServer: {{.Server.Version}}'
```

### 查看磁盘使用

```bash
# 查看 Docker 磁盘使用情况
docker system df

# 详细查看
docker system df -v

# 查看各部分占用
docker system df --format "table {{.Type}}\t{{.TotalCount}}\t{{.Size}}"
```

### 登录/登出

```bash
# 登录到 Docker Hub
docker login

# 登录到私有仓库
docker login <仓库地址>

# 示例
docker login registry.example.com

# 登出
docker logout
docker logout <仓库地址>
```

### 推送镜像

```bash
# 推送镜像到仓库
docker push <镜像名>:<标签>

# 示例
docker push my-username/my-image:1.0
docker push registry.example.com/my-image:1.0
```

---

## 清理命令

### 清理未使用的资源

```bash
# 清理所有未使用的镜像、容器、网络、卷
docker system prune

# 清理所有未使用的资源（包括停止的容器和未使用的卷）
docker system prune -a

# 清理所有未使用的资源（包括未使用的镜像）
docker system prune -a --volumes

# 只清理未使用的镜像
docker image prune

# 只清理未使用的容器
docker container prune

# 只清理未使用的网络
docker network prune

# 只清理未使用的卷
docker volume prune

# 清理构建缓存
docker builder prune

# 清理所有构建缓存
docker builder prune -a
```

### 清理示例

```bash
# 清理所有停止的容器
docker container prune

# 清理所有未使用的镜像
docker image prune -a

# 清理所有未使用的卷
docker volume prune

# 清理所有未使用的网络
docker network prune

# 清理所有构建缓存
docker builder prune -a

# 完全清理（危险）
docker system prune -a --volumes --force
```

---

## 实用技巧

### 批量操作

```bash
# 停止所有运行中的容器
docker stop $(docker ps -q)

# 删除所有已停止的容器
docker rm $(docker ps -a -q)

# 删除所有未使用的镜像
docker rmi $(docker images -f "dangling=true" -q)

# 删除所有卷
docker volume rm $(docker volume ls -q)

# 批量查看容器日志
for container in $(docker ps -q); do
    echo "=== $container ==="
    docker logs --tail 10 $container
done

# 批量重启容器
docker restart $(docker ps -q)
```

### 查找容器

```bash
# 根据名称查找容器
docker ps --filter name=my-container

# 根据镜像查找容器
docker ps --filter ancestor=nginx

# 根据状态查找容器
docker ps --filter status=exited

# 根据标签查找容器
docker ps --filter label=com.example.version=1.0

# 组合过滤
docker ps --filter name=my --filter status=running
```

### 资源限制

```bash
# 限制内存
docker run -d --memory="512m" nginx

# 限制 CPU
docker run -d --cpus="1.5" nginx

# 限制 CPU 核心数
docker run -d --cpuset-cpus="0,1" nginx

# 组合限制
docker run -d \
  --memory="1g" \
  --cpus="2.0" \
  --cpuset-cpus="0,1,2,3" \
  nginx
```

### 端口映射

```bash
# 映射单个端口
docker run -d -p 8080:80 nginx

# 映射多个端口
docker run -d -p 8080:80 -p 8443:443 nginx

# 映射到随机端口
docker run -d -p 80 nginx

# 映射到特定接口
docker run -d -p 127.0.0.1:8080:80 nginx

# 映射 UDP 端口
docker run -d -p 53:53/udp nginx
```

### 环境变量

```bash
# 设置单个环境变量
docker run -d -e KEY=VALUE nginx

# 设置多个环境变量
docker run -d -e KEY1=VALUE1 -e KEY2=VALUE2 nginx

# 从文件读取环境变量
docker run -d --env-file .env nginx

# 从宿主机传递变量
export MY_VAR="hello"
docker run -d -e MY_VAR nginx
```

### 工作目录

```bash
# 设置工作目录
docker run -d -w /app nginx

# 组合使用
docker run -d -w /app -v $(pwd):/app nginx
```

### 用户权限

```bash
# 以特定用户运行
docker run -d -u nginx nginx

# 以特定 UID 和 GID 运行
docker run -d -u 1000:1000 nginx

# 以 root 用户运行
docker run -d -u root nginx
```

### 时间设置

```bash
# 设置时区
docker run -d -e TZ=Asia/Shanghai nginx

# 挂载时区文件
docker run -d -v /etc/localtime:/etc/localtime:ro nginx
```

### 容器互联

```bash
# 使用 --link 连接容器（已弃用）
docker run -d --name db postgres
docker run -d --link db:db myapp

# 使用网络连接容器（推荐）
docker network create my-network
docker run -d --name db --network my-network postgres
docker run -d --name myapp --network my-network myapp
```

---

## 常用组合命令

### 快速启动常用服务

```bash
# Nginx
docker run -d --name nginx -p 80:80 -v $(pwd)/html:/usr/share/nginx/html nginx

# MySQL
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:8.0

# Redis
docker run -d --name redis -p 6379:6379 redis:alpine

# MongoDB
docker run -d --name mongo -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=123456 mongo

# PostgreSQL
docker run -d --name postgres -p 5432:5432 -e POSTGRES_PASSWORD=123456 postgres

# RabbitMQ
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:management

# Elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -e "discovery.type=single-node" elasticsearch:8.0.0
```

### 调试容器

```bash
# 进入容器调试
docker exec -it <容器名或ID> /bin/bash

# 查看容器日志
docker logs -f --tail 100 <容器名或ID>

# 查看容器进程
docker top <容器名或ID>

# 查看容器资源使用
docker stats <容器名或ID>

# 查看容器详细信息
docker inspect <容器名或ID>
```

### 备份与恢复

```bash
# 备份容器
docker export <容器名或ID> > backup.tar

# 备份镜像
docker save -o backup.tar <镜像名>

# 备份卷
docker run --rm -v <卷名>:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz /data

# 恢复容器
docker import backup.tar <新镜像名>:<标签>

# 恢复镜像
docker load -i backup.tar

# 恢复卷
docker run --rm -v <卷名>:/data -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /
```

---

## 快速参考表

### 镜像命令

| 命令 | 说明 |
| :--- | :--- |
| `docker search <镜像>` | 搜索镜像 |
| `docker pull <镜像>` | 拉取镜像 |
| `docker images` | 列出镜像 |
| `docker build -t <名> .` | 构建镜像 |
| `docker rmi <镜像>` | 删除镜像 |
| `docker save -o file.tar <镜像>` | 导出镜像 |
| `docker load -i file.tar` | 导入镜像 |
| `docker tag <源> <目标>` | 标记镜像 |

### 容器命令

| 命令 | 说明 |
| :--- | :--- |
| `docker run <镜像>` | 创建并启动容器 |
| `docker ps` | 列出运行中的容器 |
| `docker ps -a` | 列出所有容器 |
| `docker start <容器>` | 启动容器 |
| `docker stop <容器>` | 停止容器 |
| `docker restart <容器>` | 重启容器 |
| `docker rm <容器>` | 删除容器 |
| `docker exec -it <容器> /bin/bash` | 进入容器 |
| `docker logs <容器>` | 查看日志 |
| `docker cp <容器>:<路径> .` | 复制文件 |

### 网络命令

| 命令 | 说明 |
| :--- | :--- |
| `docker network ls` | 列出网络 |
| `docker network create <名>` | 创建网络 |
| `docker network connect <网> <容>` | 连接网络 |
| `docker network disconnect <网> <容>` | 断开网络 |
| `docker network rm <网>` | 删除网络 |

### 卷命令

| 命令 | 说明 |
| :--- | :--- |
| `docker volume ls` | 列出卷 |
| `docker volume create <名>` | 创建卷 |
| `docker volume inspect <名>` | 查看卷详情 |
| `docker volume rm <名>` | 删除卷 |
| `docker volume prune` | 清理未使用的卷 |

### 系统命令

| 命令 | 说明 |
| :--- | :--- |
| `docker info` | 查看系统信息 |
| `docker version` | 查看版本 |
| `docker system df` | 查看磁盘使用 |
| `docker system prune` | 清理未使用资源 |
| `docker stats` | 查看资源使用 |

---

## 常用参数速查

### docker run 常用参数

| 参数 | 说明 | 示例 |
| :--- | :--- | :--- |
| `-d` | 后台运行 | `docker run -d nginx` |
| `-it` | 交互式终端 | `docker run -it ubuntu bash` |
| `--name` | 指定名称 | `docker run --name my-nginx nginx` |
| `-p` | 端口映射 | `docker run -p 8080:80 nginx` |
| `-v` | 挂载卷 | `docker run -v /data:/app nginx` |
| `-e` | 环境变量 | `docker run -e KEY=VAL nginx` |
| `--rm` | 退出后删除 | `docker run --rm ubuntu bash` |
| `--restart` | 重启策略 | `docker run --restart=always nginx` |
| `-u` | 指定用户 | `docker run -u nginx nginx` |
| `-w` | 工作目录 | `docker run -w /app nginx` |

### docker exec 常用参数

| 参数 | 说明 | 示例 |
| :--- | :--- | :--- |
| `-i` | 保持输入开启 | `docker exec -i my-nginx bash` |
| `-t` | 分配终端 | `docker exec -t my-nginx bash` |
| `-d` | 后台执行 | `docker exec -d my-nginx sleep 10` |
| `-u` | 指定用户 | `docker exec -u root my-nginx bash` |
| `-w` | 工作目录 | `docker exec -w /tmp my-nginx ls` |
| `-e` | 环境变量 | `docker exec -e DEBUG=1 my-nginx bash` |

### docker logs 常用参数

| 参数 | 说明 | 示例 |
| :--- | :--- | :--- |
| `-f` | 实时跟踪 | `docker logs -f my-nginx` |
| `--tail` | 显示最后N行 | `docker logs --tail 100 my-nginx` |
| `-t` | 显示时间戳 | `docker logs -t my-nginx` |
| `--since` | 显示最近时间 | `docker logs --since 10m my-nginx` |

---

## 注意事项

### 权限问题

```bash
# Linux 系统需要 sudo
sudo docker ps
sudo docker images
sudo docker run -d nginx

# 或将用户添加到 docker 组
sudo usermod -aG docker $USER
# 需要重新登录生效
```

### 容器命名

```bash
# 使用有意义的名称
docker run -d --name web-server nginx
docker run -d --name db-server mysql
docker run -d --name cache-server redis
```

### 数据持久化

```bash
# 始终使用 -v 挂载数据
docker run -d -v mysql-data:/var/lib/mysql mysql
docker run -d -v $(pwd)/data:/app/data nginx
```

### 资源限制

```bash
# 生产环境建议设置资源限制
docker run -d \
  --memory="1g" \
  --cpus="1.0" \
  --restart=unless-stopped \
  nginx
```

### 安全建议

```bash
# 不要在命令中直接写密码
docker run -e PASSWORD=$(cat /path/to/password) myapp

# 使用只读文件系统
docker run --read-only nginx

# 使用非 root 用户
docker run -u nginx nginx
```

---

## 附录：常用镜像标签

| 镜像 | 常用标签 | 说明 |
| :--- | :--- | :--- |
| **nginx** | `latest`, `alpine`, `1.21`, `stable` | Web 服务器 |
| **mysql** | `latest`, `8.0`, `5.7` | 数据库 |
| **redis** | `latest`, `alpine`, `6.2` | 缓存 |
| **postgres** | `latest`, `14`, `13` | 数据库 |
| **node** | `latest`, `18`, `alpine` | Node.js 运行时 |
| **python** | `latest`, `3.11`, `3.10`, `alpine` | Python 运行时 |
| **ubuntu** | `latest`, `22.04`, `20.04` | Ubuntu 系统 |
| **alpine** | `latest`, `3.18` | 轻量级 Linux |
| **centos** | `latest`, `7`, `8` | CentOS 系统 |
| **debian** | `latest`, `bullseye`, `buster` | Debian 系统 |

---

**提示**：将此文档保存为书签，随时查阅 Docker 命令！