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

#### `dump` 配置

#### `fsck` 配置