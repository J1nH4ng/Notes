# [CICD-J-001] Jenkins 的安装配置

## Jenkins 服务器所需软件列表

### 运行 Jenkins 需要

- [ ] Jenkins
- [ ] Jdk 17+ （Jenkins 运行需要）

### 业务需要

- [ ] Maven
    - [ ] 修改 Maven 源
- [ ] Jdk 8 （与业务代码所需 Java 版本一致）
- [ ] NVM （管理 NodeJS 版本）
    - [ ] NodeJS 10.24.1
    - [ ] NodeJS 12.22.12
    - [ ] NodeJS 14.21.3
    - [ ] NodeJS 16.20.2
    - [ ] NodeJS 18.20.5
    - [ ] NodeJS 20.18.1
- [ ] 代码凭据配置配置（拉取代码需要）
    - [ ] SSH-Keygen
    - [ ] GitLab 的账户密码
- [ ] Php （与业务代码所需 Php 版本一致）
    - [ ] Compress
- [ ] Ansible
    - [ ] 配置账户密码
    - [ ] 配置免密执行 sudo 命令

## Jenkins 所需插件列表

1. Localization Support
2. Localization:Chinese (Simplified)
3. Locale
4. GitLab 相关
5. Blue Ocean
6. Role-based Authorization Strategy
7. Pipeline: Stage View
8. Build Pipeline
9. Version Number

## Jenkins 安装配置

### Jenkins 服务器

#### 安装与业务一致的打包环境

##### 安装 JDK 8

由于 Oracle 为了保证 JDK 的更新而强制关闭 JDK 旧版本的登录下载；所以，对于 JDK 8 的版本来说，需要我们手动从 [Oracle Java 网站](https://www.oracle.com/cn/java/technologies/downloads/)上下载，然后上传至服务器。这里我们将 JDK 8 的压缩包上传至服务器的 `/usr/local/src/` 目录下。

```bash
cd /usr/local/src
```

解压缩预编译好的 JDK 8 压缩包，并移动到外层目录中：

```bash
tar -zxvf jdk-8u371-linux-x64.tar.gz

mv jdk1.8.0_371 /usr/local/jdk8
```

写入环境变量，便于后续打包时调用：

```bash
echo 'PATH=$PATH:/usr/local/jdk8/bin
export PATH' >> /etc/profile

echo 'JAVA_HOME=/usr/local/jdk8' >> /etc/profile
```

刷新环境变量，使其生效。

```bash
source /etc/profile
```

##### 安装 Maven

Maven 的下载地址如下：[https://archive.apache.org/dist/maven/maven-3/](https://archive.apache.org/dist/maven/maven-3/)

选择最新版本进行下载即可。

对于服务器来说，可以使用 `wget` 命令进行下载，对于国内的服务器，使用清华源进行下载，这里我们下载至 `/usr/local/src` 目录下。

```bash
cd /usr/local/src

wget https://archive.apache.org/dist/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.tar.gz
```

解压缩预编译好的 Maven 压缩包，并移动到外层目录中：

```bash
tar -zxvf apache-maven-3.9.9-bin.tar.gz

cp -r /usr/local/src/apache-maven-3.9.9 /usr/local/maven3.9
```

写入环境变量，便于后续打包时调用：

```bash
echo '
PATH=$PATH:/usr/local/maven3.9/bin
export PATH' >> /etc/profile
```

刷新环境变量，使其生效。

```bash
source /etc/profile
```