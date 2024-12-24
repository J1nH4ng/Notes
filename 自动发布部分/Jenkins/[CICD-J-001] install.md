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
    - [ ] NodeJS 14.21.3
    - [ ] NodeJS 16.20.2
    - [ ] NodeJS 18.20.5
    - [ ] NodeJS 20.18.1
- [ ] 代码凭据配置配置（拉取代码需要）
    - [ ] SSH-Keygen
    - [ ] GitLab 的账户密码
- [ ] Php （与业务代码所需 Php 版本一致）
    - [ ] Compress
    
## Jenkins 插件列表

1. Blue Ocean
2. Version Number