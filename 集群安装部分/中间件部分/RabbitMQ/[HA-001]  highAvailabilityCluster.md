# [HA-001] RabbitMQ 高可用集群搭建

## 镜像队列和仲裁队列

RabbitMQ 的集群部署有两种模式：

- 普通集群：又称为标准集群，是一种分布式集群，将队列分散到集群的各个节点，提高整个集群的并发能力。
- 镜像集群：在普通集群的基础上，添加了主从备份功能，又称为主从复制集群，提高集群的数据可用性。

镜像集群虽然支持主从复制，但主从同步并不是强一致的，某些情况下可能有数据丢失的风险。因此在 RabbitMQ 的 3.8 版本以后，推出了新的功能：使用仲裁队列来代替镜像集群，底层采用 Raft 协议确保主从的数据一致性。

这里介绍的是镜像队列（仲裁队列）的安装配置方式，对于这两个队列的使用，只需要在创建 `Vhost` 时根据需要选择不同的 `Type` 即可，其中：

- `Classic`：表示创建普通队列
- `Quorum`：表示创建仲裁队列

## 安装配置

> [!WARNING]
>
> $\text{搭建镜像队列需要的服务器} \geq  3 \text{台，少于三台服务器搭建集群没有任何意义。}$

### 服务器列表

|    IP 地址     |  服务器名称  | 服务器作用 |
| :-----------: | :--------: | :-------: |
| 192.168.50.11 | RabbitMQ-1 |  Master   |
| 192.168.50.12 | RabbitMQ-2 |   Slave   |
| 192.168.60.13 | RabbitMQ-3 |   Slave   |

### 单点安装 RabbitMQ

安装 RabbitMQ 集群之前，确保每台服务器都安装了 RabbitMQ 并可以成功启动。

> [!NOTE]
>
> 如无特殊说明，接下来的所有操作需要在每一台机器上进行操作。

#### 配置 `hosts` 与 `hostname`

修改每台机器的 hostname 与服务器列表一致且将 hosts 文件配置好

```bash
hostnamectl set-hostname RabbitMQ-${i}
```

修改其中的 `${i}` 变量

将如下的内容写入每一台服务器的 `hosts` 文件中：

```bash
vim /etc/hosts
```

写入如下内容：

```text
192.168.50.11  RabbitMQ-1
192.168.50.12  RabbitMQ-2
192.168.50.13  RabbitMQ-3
```

#### 安装必要的依赖

```bash
yum -y install socat ncurses-compat-libs
```

#### 安装 Erlang 环境

RabbitMQ 是使用 Erlang 语言进行编写的，运行需要 Erlang 环境。需要注意的是 Erlang 语言版本与 RabbitMQ 版本的对应，相关的列表可以在官网[^1]进行查找。

这里使用的版本如下：

- RabbitMQ：V3.11.28
- Erlang：V25..3.2

对于 Erlang 使用 rpm 包进行安装：

```bash
cd /usr/local/src

wget https://github.com/rabbitmq/erlang-rpm/releases/download/v25.3.2/erlang-25.3.2-1.el8.x86_64.rpm

rpm -ivh erlang-25.3.2-1.el8.x86_64.rpm
```

#### 安装 RabbitMQ

下载二进制包进行解压安装：

```bash
cd /usr/local/src

wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.11.28/rabbitmq-server-generic-unix-3.11.28.tar.xz

tar xvf rabbitmq-server-generic-unix-3.11.28.tar.xz

mv /usr/local/src/rabbitmq-server-generic-unix-3.11.28 /usr/local/rabbitmq_server3.11
```

安装完成后写入环境变量：

```bash
# 添加搜索路径到配置文件
echo 'PATH=$PATH:/usr/local/rabbitmq_server3.11/sbin
export PATH' >> /etc/profile

# 刷新环境变量
source /etc/profiles
```

使用 Systemd 进行进程管理：

```bash
vim /etc/systemd/system/rabbitmq-server.service
```

写入如下内容：

```ini
[Unit]
Description=RabbitMQ broker
After=syslog.target network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/usr/local/rabbitmq_server3.11
ExecStart=/usr/local/rabbitmq_server3.11/sbin/rabbitmq-server
ExecStop=/usr/local/rabbitmq_server3.11/sbin/rabbitmqctl stop

[Install]
WantedBy=multi-user.target
```

配置开机自启动：

```bash
systemctl daemon-reload

systemctl enable --now rabbitmq-server
```

### 配置镜像集群



## 验证安装

[^1]: [RabbitMQ 不同版本对应的 Erlang 版本](https://www.rabbitmq.com/docs/which-erlang)