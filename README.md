# 入门指南

> Gos 是一个基于 Go 原生的轻量 Web 框架。

## 安装

### 服务器要求

Gos 对系统有一定要求，请确保你的服务器满足以下要求：

* Go >= 1.15

> Go 环境安装参考：[官方文档](http://docscn.studygolang.com/doc/install) 安装包：[下载](https://studygolang.com/dl)

### 安装 Gos

Gos 使用 Go Modules 来管理项目依赖。因此，在使用 Gos 之前，请确保你的机器已经配置了 Go Modules。

**配置 Go Modules**
```bash
# 开启 Go Modules
$ go env -w GO111MODULE=on

# 配置 Go Module 公共代理镜像
$ go env -w GOPROXY=https://mirrors.aliyun.com/goproxy/
```

**通过 Git 安装 Gos**
```bash
$ git clone https://github.com/zhanbai/gos.git project-name
```

**本地开发环境**

如果你在本地安装了 Go，可以通过命令行在 `http://0.0.0.0:8080` 上启动开发服务器：

```bash
$ go run main.go
```

### 配置

**配置文件**

Gos 的配置文件放在 `config` 目录中。

## 文件夹结构

默认的 Gos 应用结构旨在为不同大小的应用提供一个很好的起点。当然，您可以随意组织您的应用程序。

**api 目录**

`api` 目录包含应用程序的核心代码。`api` 目录包含 `controller` 业务接口，`model` 数据模型和 `service` 逻辑封装。处理进入应用程序请求的所有逻辑几乎都放置在此目录。

**config 目录**

`config` 目录，顾名思义，包含应用程序的配置文件。

**docker 目录**

`docker` 目录包含 Docker 镜像相关依赖文件，脚本文件等。

**docs 目录**

`docs` 目录包含项目的文档。

**library 目录**

`library` 目录包含应用程序的公共功能封装包，往往不包含业务需求逻辑。

**log 目录**

`log` 目录包含应用程序运行的相关日志。

**packed 目录**

`packed` 目录包含应用程序打包后的文件。

**router 目录**

`router` 目录包含了应用的所有路由定义。

**tests 目录**

`tests` 目录包含自动化测试文件。

**go.mod 文件**

`go.mod` 文件是使用 `Go Modules` 包管理的依赖描述文件。

## 部署

因为 Go 的可执行程序都会被编译为单独的二进制文件，所以部署 Gos 在某种程度上可以说是非常简单的。

### 独立部署

首先，我们需要使用以下命令编译项目：
```bash
$ go build -o packed/
```

如果我们的项目目录名是 `gos`，那么这个编译命令将在 `packed` 目录产生一个同名的可执行二进制文件。我们需要把 `gos` 可执行文件和 `config` 目录复制到服务器里，并将其放置到一个可以通过外部访问的地方。

接着，我们只需登录服务器，并在终端里执行以下命令，就可以运行这个服务：
```bash
$ ./gos
```

因为服务现在是在前台运行，我们将无法执行其他操作。可以使用 nohup 和 & 命令在后台运行这个服务，而且不用担心因为 HUP 信号而被杀死：
```bash
$ nohup ./gos &
```

### Supervisor 部署

为了让服务可以持续运行，我们可以使用 Supervisor 进行部署。[Supervisor](http://www.supervisord.org/) 是 Linux/Unix 系统下的一个进程管理工具。可以很方便的监听、启动、停止和重启一个或多个进程。通过 Supervisor 管理的进程，当进程意外被 Kill 时，Supervisor 会自动将它重启，可以很方便地做到进程自动恢复的目的，而无需自己编写 shell 脚本来管理进程。

**安装 Supervisor**

```bash
$ sudo apt update
$ sudo apt install supervisor
```

**创建一个配置文件**

```bash
$ sudo touch /etc/supervisor/conf.d/gos.conf 
```

编辑新建的配置文件：
```
# 新建一个应用并设置一个名称，这里设置为 gos
[program:gos]
# 设置命令在指定的目录内执行
directory=/var/www/gos/
# 这里为您要管理的项目的启动命令
command=/var/www/gos/gos
# 以哪个用户来运行该进程
user=root
# supervisor 启动时自动该应用
autostart=true
# 进程退出后自动重启进程
autorestart=true
# 进程持续运行多久才认为是启动成功
startsecs=1
# 重试次数
startretries=3
# stderr 日志输出位置
stderr_logfile=/var/www/gos/log/stderr.log
# stdout 日志输出位置
stdout_logfile=/var/www/gos/log/stdout.log
```

**启动 Supervisor**

运行下面的命令基于配置文件启动 Supervisor 程序：

```bash
$ sudo supervisord -c /etc/supervisor/supervisord.conf 
```

**使用 supervisorctl 管理项目**

```bash
# 启动 gos 应用
$ sudo supervisorctl start gos
# 重启 gos 应用
$ sudo supervisorctl restart gos
# 停止 gos 应用
$ sudo supervisorctl stop gos  
# 查看所有被管理项目运行状态
$ sudo supervisorctl status
# 重新加载配置文件
$ sudo supervisorctl update
# 重新启动所有程序
$ sudo supervisorctl reload
```
