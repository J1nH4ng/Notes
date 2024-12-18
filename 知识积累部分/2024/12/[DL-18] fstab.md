# [DL-18] Linux /etc/fstab 内容详解

## 文件内容

在默认情况下，fstab 中可能已经有了当前的分区配置，内容可能如下所示：

```
  # <file-system>                           <mount point>           <type>  <options>                  <dump> <pass>
  /dev/mapper/openeuler-root                /                       ext4    defaults                   1      1
  UUID=efed9eee-0889-47ac-b805-203d400e7a03 /boot                   ext4    defaults                   1      2
  UUID=9E04-6CFD                            /boot/efi               vfat    umask=0077,shortname=winnt 0      2
  /dev/mapper/openeuler-swap                 none                   swap    defaults                   0      0
```

如果我们新增了一个 data 分区，只需要在 fstab 文件内加入一行：

```diff
  # <file-system>                            <mount point>           <type>  <options>                  <dump> <pass>
  /dev/mapper/openeuler-root                 /                       ext4    defaults                    1      1
  UUID=efed9eee-0889-47ac-b805-203d400e7a03  /boot                   ext4    defaults                    1      2
  UUID=9E04-6CFD                             /boot/efi               vfat    umask=0077,shortname=winnt  0      2
  /dev/mapper/openeuler-swap                 none                    swap    defaults                    0      0
+ /dev/mapper/openeuler-data                 /data                   ext4    defaults                    0      0
```

## 内容解释

### `/etc/fstab` 文件的作用

`/etc/fstab` 是 Linux 系统中的一个磁盘配置文件，用于记录每次启动时需要自动挂载的设备和分区信息。该文件包括设备标识、挂载点、文件系统类型、挂载参数等配置内容。

### 配置文件详解

从文件内容中可以看出，每一行由六个部分组成，从左往右分别是：

#### 文件系统 `file-system`

要挂载的分区或者存储设备，可选值为：

- 设备名称
- LABEL
- UUID
- 伪文件系统名称

#### 挂载的位置

即上述文件系统需要挂载的位置，如 `/data` 目录

#### 文件类型

要挂载设备或是分区的文件系统类型，支持许多种不同的文件系统，可选值为：

- ext2
- ext3
- ext4
- reiserfs
- xfs
- jfs
- smbfs
- iso9660
- vfat
- ntfs
- swao
- auto

设置为 `auto` 时，`mount` 命令会自动猜测使用的文件系统类型，对于 CDROM 和 DVD 等移动设备是非常有用的

#### 挂载参数

挂载时使用的参数，有些 `mount` 参数是特定的文件系统才拥有的。

常用的参数如下：

- `auto`：在启动时或键入 `mount -a` 命令时自动挂载
- `noauto`：只在输入命令时才会被挂载
- `exec`：允许执行此分区的二进制文件
- `noexec`：不允许执行此分区的二进制文件
- `ro`：以只读模式挂载文件系统
- `rw`：以读写模式挂载文件系统
- `user`：允许任意用户挂载此文件系统
- `users`：允许所有 users 组中的用户挂载文件系统
- `nouser`：只能被 root 用户挂载
- `owner`：只能被设备所有者挂载
- `sync`：I/O 同步进行
- `async`：I/O 异步进行
- `dev`：解析文件系统上的块特殊设备
- `nodev`：不解析文件系统上的块特殊设备
- `suid`：允许 suid 操作和设定 sgid 位。这一参数通常用于一些特殊任务，使一般用户运行程序时临时提升权限
- `nosuid`：禁止 suid 操作和设定 sgid 位
- `noatime`：不更新文件系统上 inode 访问记录，可以提升性能 (参见 atime 参数)
- `nodiratime`：不更新文件系统上的目录 inode 访问记录，可以提升性能 (参见 atime 参数)
- `relatime`：实时更新 inode access 记录。只有在记录中的访问时间早于当前访问才会被更新。（与 noatime 相似，但不会打断如 mutt 或其它程序探测文件在上次访问后是否被修改的进程。），可以提升性能 (参见 atime 参数)
- `flush`：vfat 的选项，更频繁地刷新数据，复制对话框或进度条在全部数据都写入后才消失
- `defaults`：使用文件系统的默认挂载参数，例如 ext4 的默认参数为:rw, suid, dev, exec, auto, nouser, async

#### `dump` 配置

`dump` 工具通过它决定何时做备份。`dump` 会检查其内容，并根据数字来决定是否对这个文件系统进行备份。

值为：

- 0：表示忽略
- 1：代表每天进行 `dump` 备份
- 2：代表不定日期的进行 `dump` 备份

#### `fsck` 配置

`fsck` 读取该数值来决定是否需要检查文件系统

值为：

- 0：不自检
- 1：最先自检
- 2：自检，数字越小，优先级越大

同时设置为 1 时，表示同时自检