# [BS-001] 初始化服务器

## 检查列表

- [ ] 虚拟机配置选项是否为高可用选项
- [x] 是否为静态 IP 地址
- [x] 配置特定的 DNS
- [ ] 下载源是否为国内源
- [ ] 时钟同步
- [ ] 防火墙配置
    - [ ] 关闭防火墙
    - [ ] 放开特定端口以及特定 IP 访问
- [ ] 关闭 SeLinux
- [ ] 升级 OpenSSL
    - [ ] 版本确定
- [ ] 升级 OpenSSH
    - [ ] 9.8p1 版本及以上
    - [ ] 禁用特定算法，修复 CVE-2002-20001 漏洞
- [ ] 配置最大文件打开数
- [ ] 配置内核和网络参数
- [ ] 配置 LVM 磁盘
- [ ] 测试硬件是否符合要求
    - [ ] 硬盘是否为 SSD
    

## 具体操作

> 这里的服务器操作系统都为 RedHat 系列

### 配置静态 IP 地址

网卡的配置地址如下：

```bash
cd /etc/sysconfig/network-scripts
```

修改当前出网的网卡配置，如果不知道可以使用 `ip addr` 命令进行查看：

```bash
vim ifcfg-ens33
```

写入如下内容：

```diff
DEVICE=ens192
NAME=ens192
UUID=4acccc84-3182-4157-994d-329d8033dde2
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
- BOOTPROTO=dynamic
+ BOOTPROTO=static
+ IPADDR=10.1.0.123
+ PREFIX=24
+ GATEWAY=10.1.0.254
+ DNS1=8.8.8.8
+ DNS2=114.114.114.114
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
ONBOOT=yes
```

修改完成后，重启服务器：

```bash
reboot
```