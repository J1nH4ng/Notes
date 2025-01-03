# [DL-03] Systemd 的 Service 文件详解

## Service 文件的组成部分

systemd 服务的内容主要分为三个部分，控制单元 `[Unit]`，服务 `[Service]` 以及 `[Install]` 安装部分。

### [Unit] 字段

这个字段主要给出服务描述、启动顺序和依赖关系，其中的字段内容如下：

- `Description=`：给出当前服务的简单描述
- `Documentation=`：给出文档位置
- `After=`：表示在什么服务之后启动
- `Before=`：表示在什么服务之前启动
- `Wants=`：表示该服务和某服务存在某种弱依赖关系，即某服务停止运行或退出也不影响该服务继续运行
- `Requires=`：表示强依赖关系，即某服务停止运行或提出，该服务也必须停止运行

> [!NOTE]
> - `After` 和 `Before` 只设计启动顺序，和依赖关系无关
> - `Wants` 和 `Requires` 只涉及依赖关系，和启动顺序无关，默认情况下是同时启动的

### [Server] 字段

这个字段主要给出服务的启动行为，如何启动、重启、停止，其字段内容如下：

- `Type=`：定义启动类型。它可以设置的值如下：
  - `simple`：默认值，`ExecStart` 字段启动的进程为主进程
  - `forking`：`ExecStart` 字段将以 `fork()` 方式启动，此时父进程将会退出，子进程将会成为主进程。
  - `oneshot`：类似于 `simple`，但是只执行一次，Systemd 会等待它执行完成，才启动其他服务。
  - `dbus`：类似于 `simple`，但会等待 D-Bus 信号后启动。
  - `notify`：类似于 `simple`，启动结束后会发出通知信号，然后 Systemd 再启动其他服务。
  - `idle`：类似于 `simple`，但是要等到其他任务都执行完，才会启动该服务。一种使用场合是为让该服务的输出不与其他服务的输出相混合。
- `ExecStart=`：定义启动进程时执行的命令，就是手动启动时执行的命令。
- `ExecReload=`：重启服务时执行的命令
- `ExecStop=`：停止服务时执行的命令
- `ExecStartPre=`：启动服务之前执行的命令
- `ExecStartPost=`：启动服务之后执行的命令
- `ExecStopPost=`：停止服务之后执行的命令
- `KillMode=`：定义 Systemd 如何停止服务。它可以设置的值为：
  - `control-group`：默认值，当前控制组里面的所有子进程，都会被杀掉。
  - `process`：只杀死主进程。
  - `mixed`：主进程将接收到 SIGTERM 信号，子进程接收到 SIGKILL 信号。
  - `none`：没有进程会被杀掉，只是执行服务的 stop 命令。
- `Restart=`：定义了服务退出后，Systemd 的重启方式。它可以设置的值如下：
  - `no`：默认值，退出后不会重启。
  - `on-success`：只有正常退出时（退出状态码为 0），才会被重启。
  - `on-failure`：非正常退出时（退出状态码不为 0），包括被信号终止和超时，才会重启。
  - `on-abnormal`：只有被信号终止和超时，才会重启。
  - `on-abort`：只有在收到没有捕捉到的信号终止时，才会重启。
  - `on-watchdog`：超时退出，才会重启。
  - `always`：不管什么原因退出，总是重启。
- `RestartSec=`：在 Systemd 重启服务之前，需要等待的秒数。
- `User=`：表示设置服务的用户名。
- `WorkingDirectory=`：指定服务的安装目录。

### [Install] 字段

该字段定义如何安装这个配置文件，即怎么样做到开机自启动。其中的字段内容如下：

- `WantedBy=`：表示服务所在的 Target。

Target 的含义时服务器组，即表示一组服务。

`WantedBy=multi-user.target` 指的是服务所在的 Target 为 multi-user，在这个组里的服务，都将开机自启动。

可以使用一下命令，查看 `multi-user.target` 内所包含的服务：

```bash
systemctl list-dependencies multi-user.target
```