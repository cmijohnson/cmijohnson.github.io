---
title: Docker 基础入门教程
date: 2026-04-22 22:41:46
tags: [docker]
categories: 基础知识
---

# Docker 基础入门教程

![img](https://usual1009.oss-cn-shanghai.aliyuncs.com/img/20260116155541617.png)

Docker 让开发者可以打包应用及其依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 或 Windows 机器上。本教程将以最通俗易懂的方式，带你掌握 Docker 的核心用法。

---

## 一、 Docker 环境安装

### 1. Windows 系统
在 Windows 上，我们通常安装 **Docker Desktop**。
*   **下载地址**：[Docker 官网](https://www.docker.com/products/docker-desktop/)
*   **注意项**：安装时请确保勾选了 **WSL 2** 支持，这会让 Docker 运行得更加顺畅。
*   **验证**：打开终端输入 `docker --version`，看到版本号即安装成功。

### 2. Linux 系统（以 Ubuntu 为例）
Linux 下通常使用命令行安装，最快的方法是使用官方的一键脚本：(选择1,4的命令进行安装)

```php
# To install the latest stable versions of Docker CLI, Docker Engine, and their
# dependencies:
#
# 1. download the script
#
#   $ curl -fsSL https://get.docker.com -o install-docker.sh
#
# 2. verify the script's content
#
#   $ cat install-docker.sh
#
# 3. run the script with --dry-run to verify the steps it executes
#
#   $ sh install-docker.sh --dry-run
#
# 4. run the script either as root, or using sudo to perform the installation.
#
#   $ sudo sh install-docker.sh
```

记得使用科学的上网方式进行安装哦

### **pull异常 教程 切换镜像站**

#### 一键配置脚本

```bash
bash <(wget -qO- https://xuanyuan.cloud/docker.sh)
```

**支持系统：**OpenCloudOS、Ubuntu、Debian、CentOS、RHEL、Rocky Linux 等主流发行版。

---

## 二、 核心运行参数详解 (docker run)

在使用 Docker 时，最常用的命令就是 `docker run`。这个命令后面经常跟着一串“英文字母”参数，理解这些参数是入门的关键。

![image-20260116161801658](https://usual1009.oss-cn-shanghai.aliyuncs.com/img/20260116161801764.png)

以下是常用参数的深度解析：

### 1. `-i`：交互式操作 (interactive)
*   **通俗解释**：保持容器的标准输入（STDIN）开启。
*   **为什么要用**：如果你想在容器启动后输入命令，就必须带上这个参数。它就像是连接了容器的“麦克风”，让它能听见你说话。

### 2. `-t`：终端 (tty)
*   **通俗解释**：为容器分配一个伪终端。
*   **为什么要用**：它会为你提供一个类似于 Linux 终端的命令行界面。通常与 `-i` 连用（`-it`），让你感觉就像直接登录进了另一台电脑的黑色窗口。

### 3. `-d`：后台运行 (detach)
*   **通俗解释**：容器启动后会在后台运行，不会占用你当前的终端窗口。
*   **为什么要用**：当你运行一个网站服务器（如 Nginx）或数据库（如 MySQL）时，你不希望它一直霸占着你的屏幕，这时就用 `-d`。

### 4. `-p`：端口映射 (publish)
*   **格式**：`-p 宿主机端口:容器内端口`
*   **通俗解释**：在容器的“围墙”上开扇窗户。
*   **为什么要用**：容器内部是一个隔离的网络。如果你在容器内部运行了一个 80 端口的 Web 服务，外部是访问不到的。通过 `-p 8080:80`，你可以访问电脑的 `localhost:8080`，流量会自动转发到容器内部的 80 端口。

### 5. `--name`：指定容器名称
*   **通俗解释**：给你的容器起个“绰号”。
*   **为什么要用**：如果不指定，Docker 会随机给它起一个类似 `determined_shirley` 的名字。指定名字（如 `--name my-web`）方便你后续停止或删除它。

### 6. `-v` : 挂载卷

在掌握了基础的容器运行（`-itd`）和端口映射（`-p`）后，另一个至关重要的参数就是 **`-v`**。如果你不学会使用 `-v`，那么你的 Docker 容器就像一个没有硬盘的临时电脑，一旦关机（删除容器），所有数据都会灰飞烟灭。

---

#### 1️⃣、 `-v` 是什么？

**`-v` 代表 Volume（卷）**，也常被称为 **“挂载”**。

*   **通俗解释**：它就像是在你的宿主机（真实的 Windows 或 Linux 电脑）和 Docker 容器之间拉了一根“传送带”或者建立了一个“共享文件夹”。
*   **核心功能**：将宿主机的目录或文件，映射到容器内部。

---

#### 2️⃣、 为什么要用 `-v`？（解决两大痛点）

###### 1. 数据持久化（防止丢失）

默认情况下，你在容器里创建的任何文件（比如数据库里的数据、用户上传的图片）都保存在容器的层中。**一旦你执行了 `docker rm` 删除了容器，这些数据会全部消失。**
通过 `-v`，你把数据存在宿主机上，即使容器删了，新建一个容器挂载同一个目录，数据依然在。

###### 2. 修改方便（实时同步）

如果你想修改容器里的配置文件（比如 Nginx 的 `index.html`），你不需要每次都进入容器内部去写代码。你只需要在外面（Windows/Linux）修改那个挂载的文件夹，容器内部会**实时同步**生效。

---

#### 3️⃣、 `-v` 的语法格式

```bash
-v [宿主机绝对路径]:[容器内绝对路径]:[权限]
```

###### 1. 宿主机路径 (Host Path)

你电脑上的真实路径，例如 `D:\web_data` (Windows) 或 `/home/user/app` (Linux)。

###### 2. 容器内路径 (Container Path)

镜像中预设的路径，例如 Nginx 存放网页的 `/usr/share/nginx/html`。

###### 3. 权限 (可选)

*   `rw`: 读写（默认）。
*   `ro`: 只读（read-only）。如果你希望容器只能看文件，不能改文件，就加上 `:ro`。

---

#### 4️⃣、 实战案例：一分钟修改网页内容

假设我们想运行一个 Nginx 容器，但不想用它默认的欢迎页面，而是用我们自己电脑上的 HTML 文件。

##### 1. 在宿主机创建文件

在 Linux 的 `/root/html` 目录下（或 Windows 的某个文件夹下）创建一个 `index.html`，内容写上 `<h1>Hello Docker Volume!</h1>`。

##### 2. 运行挂载命令

```bash
docker run -d \
  --name my-nginx-web \
  -p 8080:80 \
  -v /root/html:/usr/share/nginx/html \
  nginx
```

**命令拆解：**

*   **`-d`**: 后台运行。
*   **`-p 8080:80`**: 电脑 8080 端口映射容器 80 端口。
*   **`-v /root/html:/usr/share/nginx/html`**: 
    *   **把外部的** `/root/html` 文件夹。
    *   **映射到内部的** `/usr/share/nginx/html`。
*   **`nginx`**: 镜像名。

##### 3. 验证结果

现在你访问 `http://localhost:8080`，看到的不再是 Nginx 默认页面，而是你刚才写的 `Hello Docker Volume!`。
**神奇之处：** 此时你修改宿主机 `/root/html/index.html` 里的内容，刷新浏览器，页面会立刻变化，无需重启容器！

---

#### 5️⃣、 匿名卷与具名卷（进阶）

除了上面这种指定具体路径的“绑定挂载”（Bind Mount），Docker 还有两种常见方式：

1. **匿名卷**：
   `-v /data` 
   （只写了容器内路径。Docker 会自动在宿主机深处创建一个随机名字的文件夹。通常用于不关心数据存在哪，只要能持久化就行的情况。）

2. **具名卷**：
   `-v my_db_data:/var/lib/mysql`
   （`my_db_data` 是一个名字。Docker 会管理这个卷。你可以通过 `docker volume ls` 看到它。这种方式最推荐，因为它由 Docker 统一管理，方便备份和迁移。）

   你要学会管理你的“具名卷”，主要靠这几个指令：

   | 命令                               | 作用                                                         |
   | :--------------------------------- | :----------------------------------------------------------- |
   | **`docker volume ls`**             | 查看当前所有的命名卷（看看有哪些保险柜）                     |
   | **`docker volume inspect <卷名>`** | 查看这个卷在硬盘的什么位置（通常在 `/var/lib/docker/volumes/`） |
   | **`docker volume create <卷名>`**  | 手动创建一个卷（可选，通常运行 `run` 时会自动创建）          |
   | **`docker volume rm <卷名>`**      | 删除一个卷（注意：删了数据就真没了）                         |
   | **`docker volume prune`**          | 删除所有没在使用的卷（清理空间）                             |

---

```bash
# 默认情况（不加 -a）：
docker volume prune
# 只删除未被任何容器引用的卷

# 使用 -a 选项：
docker volume prune -a
# 删除所有未使用的卷，包括：
# 1. 未被引用的卷
# 2. 匿名卷
# 3. 悬空卷
```

##### 实战演示：如何使用命名卷

###### 第一步：运行容器并绑定命名卷

你不需要手动创建卷，直接运行命令，Docker 发现卷不存在会自动创建：

```bash
# 运行一个 Nginx，把网页存到名为 my_web_data 的卷里
docker run -d \
  --name my_nginx \
  -p 8081:80 \
  -v my_web_data:/usr/share/nginx/html \
  nginx
```

###### 第二步：查看卷是否生成

```bash
docker volume ls
```

你会看到列表中多了一个 `my_web_data`。

###### 第三步：查看卷的真实位置（仅限 Linux）

如果你想知道 Docker 把数据存哪了：

```bash
docker volume inspect my_web_data
```

在输出的 `Mountpoint` 字段，你会看到类似 `/var/lib/docker/volumes/my_web_data/_data` 的路径。

------

###### 5. 常见困惑：命名卷 vs 绑定挂载

| 特性         | 命名卷 (`-v my_vol:/app`)    | 绑定挂载 (`-v /root/data:/app`) |
| :----------- | :--------------------------- | :------------------------------ |
| **存储位置** | Docker 管理的特定区域        | 你自己指定的任何地方            |
| **管理方式** | 通过 Docker 命令管理         | 通过文件浏览器或系统命令管理    |
| **适用场景** | 数据库存储、持久化配置文件   | 开发环境（需要实时改代码）      |
| **对初学者** | **更推荐（不容易写错路径）** | 需要搞清楚宿主机绝对路径        |

#### 6️⃣、 小结

| 参数项 | 全称       | 作用         | 就像是...                |
| :----- | :--------- | :----------- | :----------------------- |
| **-v** | **Volume** | **数据挂载** | 给容器插了一块“外接硬盘” |

**使用建议：**

*   **数据库（MySQL/Redis）**：一定要用 `-v`，否则删容器等于删库跑路。
*   **配置文件**：建议用 `-v`，方便在外面改配置。
*   **日志文件**：建议用 `-v`，方便在宿主机直接查看日志，不用进容器。

***

*注意：尽管 `-v` 是 Docker 的标准操作，但在 Windows 环境下使用 WSL 2 挂载路径时，可能会遇到文件权限或路径格式转换的问题，操作时请留意报错信息。*

---

### 7. `-e` : **设置环境变量**

#### **作用：**

向容器内部**传递环境变量**，常用于配置应用程序。

#### **基本语法：**

```bash
docker run -e KEY=VALUE <镜像>
```

#### **使用示例：**

```bash
# 设置单个环境变量
docker run -d -e MYSQL_ROOT_PASSWORD=123456 mysql

# 设置多个环境变量
docker run -d \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -e MYSQL_DATABASE=mydb \
  -e MYSQL_USER=admin \
  mysql

# 从文件读取环境变量
docker run -d --env-file .env mysql
```

#### 📊 **实际应用场景**

##### **场景1：运行数据库**

```bash
# MySQL容器需要root密码
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=mysecretpassword \
  -e MYSQL_DATABASE=appdb \
  -p 3306:3306 \
  mysql:8.0
```

##### **场景2：运行Web应用**

```bash
# Node.js应用配置
docker run -d \
  --name myapp \
  -e NODE_ENV=production \
  -e DATABASE_URL=postgres://user:pass@db:5432/db \
  -e REDIS_URL=redis://redis:6379 \
  -p 3000:3000 \
  my-node-app
```

##### **场景3：运行Redis**

```bash
# Redis设置密码
docker run -d \
  --name redis-cache \
  -e REDIS_PASSWORD=myredispass \
  -p 6379:6379 \
  redis:alpine
```

#### 🔧 **组合使用示例**

```bash
# 典型组合：后台运行 + 环境变量 + 端口映射
docker run -d \
  --name webapp \
  -e APP_ENV=production \
  -e DB_HOST=mysql \
  -p 80:80 \
  -p 443:443 \
  nginx:latest

# 分解：
# -d          → 后台运行
# --name      → 指定容器名称
# -e          → 设置环境变量
# -p          → 端口映射
```

#### 📋 **常用组合模式**

| 场景     | 典型命令                            | 说明           |
| :------- | :---------------------------------- | :------------- |
| 快速测试 | `docker run -it --rm alpine sh`     | 交互式临时容器 |
| 后台服务 | `docker run -d -p 80:80 nginx`      | Web服务器      |
| 数据库   | `docker run -d -e VAR=val mysql`    | 需要配置的服务 |
| 开发环境 | `docker run -d -v $(pwd):/app node` | 挂载代码目录   |

#### 💡 **实用技巧**

##### **1. 查看容器环境变量**

```bash
# 查看已设置的环境变量
docker exec <容器> env

# 查看单个变量
docker exec <容器> printenv NODE_ENV
```

##### **2. 动态修改环境变量**

```bash
# 创建时忘记设置？需要重新创建
docker stop <容器>
docker rm <容器>
docker run -d -e NEW_VAR=value ...  # 重新创建
```

##### **3. 使用环境变量文件**

```bash
# .env 文件内容：
DB_PASSWORD=secret123
API_KEY=abc123xyz

# 启动时引用
docker run -d --env-file .env myapp
```

#### ⚠️ **注意事项**

##### **`-e` 的注意事项：**

1. **敏感信息**：不要在命令中直接写密码（会出现在history中）

   ```bash
   # 不安全（密码在history中可见）
   docker run -e PASSWORD=123456 app
   
   # 相对安全（从变量读取）
   export DB_PASS=123456
   docker run -e PASSWORD=$DB_PASS app
   
   # 最安全（使用Docker Secrets或环境文件）
   docker run --env-file .secrets app
   ```

2. **变量覆盖**：容器内已有的环境变量会被覆盖

3. **特殊字符**：包含空格或特殊字符的值需要引号

   ```bash
   docker run -e GREETING="Hello World" app
   ```

##### 🆚 **`-it` 与 `-d` 对比**

| 选项  | 用途       | 适用场景                    |
| :---- | :--------- | :-------------------------- |
| `-it` | 交互式终端 | 调试、进入容器、执行命令    |
| `-d`  | 后台运行   | 长期运行的服务（Web、DB等） |

```bash
# 组合使用：先调试，后后台运行
docker run -it --rm alpine sh      # 调试镜像
docker run -d --name myapp alpine  # 正式运行
```

- **`-d` \**→\** D**etached（分离）→ 后台运行
- **`-e` \**→\** E**nvironment（环境）→ 环境变量
- **`-it` \**→\** I\**nteractive +\** T**TY → 交互式终端

### 8. `--name` : **自定义容器名称**

```bash
docker run --name <容器名称> <镜像>
```

##### 📚 优点

**不使用 --name 的情况：**

```bash
docker run nginx
# Docker会自动生成一个随机名称，如：
# romantic_babbage
# angry_goldberg
# sleepy_curie
```

**使用 --name 的好处：**

1. **易于记忆**：`my-nginx` 比 `romantic_babbage` 好记
2. **便于管理**：停止、启动、删除时不用记ID
3. **避免冲突**：明确知道每个容器的用途
4. **脚本友好**：在脚本中使用名称而不是ID

##### 🔧 **基本用法**

###### **1. 给容器起名字**

```bash
# 运行一个名为 "web-server" 的nginx容器
docker run -d --name web-server nginx

# 运行一个名为 "mysql-db" 的MySQL容器
docker run -d --name mysql-db -e MYSQL_ROOT_PASSWORD=123456 mysql
```

###### **2. 使用名称管理容器**

```bash
# 停止容器
docker stop web-server

# 启动容器
docker start web-server

# 重启容器
docker restart web-server

# 查看容器日志
docker logs web-server

# 进入容器
docker exec -it web-server bash

# 删除容器
docker rm web-server
```

###### 综合实战演示

让我们把上面的参数串起来，运行一个 Nginx（网页服务器）容器：

```bash
docker run -itd --name my-nginx -p 8888:80 nginx
```

**这段命令的意思是：**
1.  **`docker run`**: 启动一个新容器。
2.  **`-itd`**: 结合了交互、终端和后台运行。意味着它在后台活着，但如果你想进入它内部，它也为你准备好了终端。
3.  **`--name my-nginx`**: 给容器起名叫 `my-nginx`。
4.  **`-p 8888:80`**: 把容器里的 80 端口映射到我电脑的 8888 端口。
5.  **`nginx`**: 使用的镜像名称。

执行完后，你在浏览器输入 `localhost:8888` 就能看到 Nginx 的欢迎页面了。

### 9. `--restart` : 重启策略

```bash
docker run --restart <策略> <镜像>
```

##### 📚 **四种重启策略**

**1. `no` - 不自动重启（默认）**

bash

```bash
docker run --restart no nginx
# 或省略（默认就是no）
docker run nginx
```

**特点：**

- 容器退出后**不会自动重启**
- 适用于一次性任务、测试容器

**2. `always` - 总是重启**

bash

```bash
docker run --restart always nginx
```

**特点：**

- 容器退出时**总是重启**
- 无论退出状态码是什么（0或非0）
- 适用于必须保持运行的服务

**3. `on-failure` - 失败时重启**

bash

```bash
docker run --restart on-failure nginx
# 或指定最大重试次数
docker run --restart on-failure:5 nginx
```

**特点：**

- 只有**非0状态码退出**时才重启
- 可以指定最大重试次数
- 适用于可能出错但需要恢复的服务

**4. `unless-stopped` - 除非手动停止**

bash

```bash
docker run --restart unless-stopped nginx
```

**特点：**

- 容器退出时重启，**除非用户手动停止**
- Docker守护进程重启后，容器也会自动启动
- **最常用**的生产环境策略

##### 📊 **策略对比表**

| 策略             | 退出时重启 | Docker重启时启动 | 手动停止后 | 适用场景           |
| :--------------- | :--------- | :--------------- | :--------- | :----------------- |
| `no`             | ❌ 否       | ❌ 否             | -          | 测试、一次性任务   |
| `always`         | ✅ 总是     | ✅ 是             | ✅ 会重启   | 必须持续运行的服务 |
| `on-failure`     | ⚠️ 失败时   | ✅ 是             | -          | 可能出错的服务     |
| `unless-stopped` | ✅ 总是     | ✅ 是             | ❌ 不重启   | **生产环境推荐**   |

---

## 四、 常用管理命令

### 1. 查看容器状态
*   `docker ps`: 查看当前正在运行的容器。
*   `docker ps -a`: 查看所有容器（包括已经停止的）。

### 2. 停止与启动
*   `docker stop my-nginx`: 停止名为 `my-nginx` 的容器。
*   `docker start my-nginx`: 再次启动它。

### 3. 进入容器内部
如果你想进到容器里修改文件，使用：
```bash
docker exec -it my-nginx /bin/bash
```

### 4. 删除容器与镜像
*   `docker rm -f my-nginx`: 强制删除运行中的容器。
*   `docker rmi nginx`: 删除本地的 nginx 镜像。

---

## 五、 Docker 常用命令速查表

### 📋 核心命令对比表

| 命令                 | 作用             | 常用选项                                                     | 示例                               | 说明                            |
| :------------------- | :--------------- | :----------------------------------------------------------- | :--------------------------------- | :------------------------------ |
| **`docker pull`**    | 拉取镜像         | `-a`：所有标签 `--platform`：指定平台                        | `docker pull nginx:alpine`         | 只下载镜像，不运行              |
| **`docker create`**  | 创建容器         | `--name`：命名 `-it`：交互终端 `-p`：端口映射 `-v`：卷挂载 `-e`：环境变量 | `docker create --name myapp nginx` | 只创建，不启动                  |
| **`docker run`**     | 创建并启动容器   | `-d`：后台运行 `--rm`：退出后删除 `--restart`：重启策略      | `docker run -d -p 80:80 nginx`     | **最常用**，等于 create + start |
| **`docker start`**   | 启动已存在的容器 | `-a`：附加输出 `-i`：交互模式                                | `docker start myapp`               | 启动已停止的容器                |
| **`docker stop`**    | 停止运行中的容器 | `-t`：超时时间（秒）                                         | `docker stop myapp`                | 优雅停止（SIGTERM）             |
| **`docker restart`** | 重启容器         | `-t`：超时时间                                               | `docker restart myapp`             | 等于 stop + start               |
| **`docker pause`**   | 暂停容器         | 无                                                           | `docker pause myapp`               | 暂停所有进程                    |
| **`docker unpause`** | 恢复容器         | 无                                                           | `docker unpause myapp`             | 恢复暂停的容器                  |
| **`docker rm`**      | 删除容器         | `-f`：强制删除运行中的 `-v`：同时删除卷                      | `docker rm -f myapp`               | 删除容器（镜像还在）            |
| **`docker rmi`**     | 删除镜像         | `-f`：强制删除                                               | `docker rmi nginx`                 | 删除镜像                        |

### 🔄 容器生命周期管理

#### **完整流程示例：**
```bash
# 1. 拉取镜像
docker pull nginx:alpine

# 2. 创建容器（不启动）
docker create --name web -p 80:80 nginx:alpine

# 3. 启动容器
docker start web

# 4. 停止容器
docker stop web

# 5. 重启容器
docker restart web

# 6. 删除容器
docker rm web
```

#### **快捷方式：**
```bash
# 等于上面1-3步
docker run -d --name web -p 80:80 nginx:alpine
```

### 📊 状态转换图

```
docker pull
    ↓
[镜像仓库] → (镜像本地缓存)
    ↓
docker create → [容器创建] → docker start → [运行中]
    ↓                                   ↓
docker rm ← [已停止] ← docker stop      docker pause → [已暂停]
                                          ↓
                                        docker unpause
```

### 🎯 常用组合命令

#### **开发测试：**
```bash
# 临时运行，退出即删除
docker run -it --rm alpine sh

# 拉取最新镜像并运行
docker pull node:latest && docker run node
```

#### **生产部署：**
```bash
# 完整生产级容器
docker run -d \
  --name app-prod \
  --restart unless-stopped \
  -p 80:80 \
  -v app-data:/data \
  -e NODE_ENV=production \
  myapp:latest
```

#### **批量操作：**
```bash
# 停止所有容器
docker stop $(docker ps -q)

# 删除所有停止的容器
docker container prune

# 删除所有未使用的镜像
docker image prune -a
```

### ⚙️ 选项详解表

| 选项            | 适用命令                | 作用       | 示例                                |
| --------------- | ----------------------- | ---------- | ----------------------------------- |
| **`-d`**        | `run`                   | 后台运行   | `docker run -d nginx`               |
| **`-it`**       | `run`, `create`, `exec` | 交互终端   | `docker run -it alpine sh`          |
| **`--name`**    | `run`, `create`         | 容器命名   | `docker run --name myapp nginx`     |
| **`-p`**        | `run`, `create`         | 端口映射   | `docker run -p 8080:80 nginx`       |
| **`-v`**        | `run`, `create`         | 卷挂载     | `docker run -v /data:/app nginx`    |
| **`-e`**        | `run`, `create`         | 环境变量   | `docker run -e KEY=value nginx`     |
| **`--rm`**      | `run`                   | 退出后删除 | `docker run --rm alpine echo hi`    |
| **`--restart`** | `run`, `create`         | 重启策略   | `docker run --restart always nginx` |
| **`-f`**        | `rm`, `rmi`             | 强制操作   | `docker rm -f myapp`                |
| **`-a`**        | `ps`, `start`, `pull`   | 所有/附加  | `docker ps -a`                      |

### 🔍 查看和监控

| 命令             | 作用         | 常用选项                                             |
| ---------------- | ------------ | ---------------------------------------------------- |
| `docker ps`      | 查看容器     | `-a`：所有容器<br>`-q`：只显示ID<br>`--filter`：过滤 |
| `docker images`  | 查看镜像     | `-a`：所有镜像<br>`-q`：只显示ID                     |
| `docker logs`    | 查看日志     | `-f`：实时跟踪<br>`--tail N`：最后N行                |
| `docker inspect` | 查看详情     | `-f`：格式化输出                                     |
| `docker stats`   | 实时资源监控 | 无                                                   |
| `docker top`     | 查看容器进程 | 无                                                   |
| `docker diff`    | 查看文件变化 | 无                                                   |

### 💡 实用技巧

#### **1. 一键清理**
```bash
# 清理所有未使用的资源
docker system prune -a

# 清理卷
docker volume prune
```

#### **2. 查看帮助**
```bash
# 查看命令帮助
docker run --help

# 查看Docker版本
docker version

# 查看系统信息
docker info
```

#### **3. 导出和导入**
```bash
# 导出容器为镜像
docker commit myapp myapp:backup

# 导出镜像为文件
docker save myapp:latest > myapp.tar

# 从文件导入镜像
docker load < myapp.tar
```

### 🚨 注意事项

1. **`docker run` ≠ `docker create + start`**
   - `run` 会应用所有运行时配置
   - `create` 只创建，可以后续修改配置

2. **停止 vs 删除**
   - `stop`：容器还在，可以重新启动
   - `rm`：容器被删除，数据可能丢失（除非用了卷）

3. **镜像 vs 容器**
   - `pull/rmi`：操作的是**镜像**（模板）
   - `run/stop/rm`：操作的是**容器**（实例）

4. **生产环境建议**
   ```bash
   # 总是使用 --name
   # 总是使用 --restart unless-stopped
   # 重要数据一定要用 -v 挂载卷
   ```

### 📱 快速参考卡片

```bash
# 最常用组合（记住这个就够了）：
docker run -d --name <名称> -p <端口> -v <卷> --restart unless-stopped <镜像>

# 示例：
docker run -d --name web -p 80:80 -v html:/usr/share/nginx/html --restart unless-stopped nginx
```

这个表格涵盖了 Docker 日常使用中 **90%** 的场景。掌握这些命令，你就能熟练管理 Docker 容器了！

## 六、 总结

Docker 的核心逻辑就是：**拉取镜像 -> 运行容器 -> 管理容器**。

*   **镜像**是“安装包”。
*   **容器**是安装好后“正在运行的程序”。
*   **参数**（`-i`, `-t`, `-d`, `-p`）是用来控制这个程序运行方式的“开关”。

![Image](https://usual1009.oss-cn-shanghai.aliyuncs.com/img/20260116155604624.png)