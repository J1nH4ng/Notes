# [INFRA-001-代码仓库]

## OpenEuler 上安装 GitLab

### 物理机安装

####  安装 GitLab

1. 使用如下命令安装依赖

    ```bash
    yum install -y curl policycoreutils-python-utils perl perl-devel openssh-server libxcrypt libxcrypt-devel
    ```

2. 下载离线安装包

    [GitLab 清华源地址](https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/)

3. 下载完成后，上传至服务器，并使用如下命令进行安装

    ```bash
    EXTERNAL_URL="https://<your-domain-name>.com" rpm -ivh gitlab-ce-17.3.1-ce.0.el9.x86_64.rpm
    ```

    可以使用如下命令查看安装进度：
    
    ```bash
   gitlab-ctl tail
    ```
    
    在安装过程中可能出现无法找到 `lubcrypto.so.1.1.0` 这个 lib 库的问题，这是因为新的发行版将其改名为 `libcrypto.so.2` 导致的，添加软链即可解决问题：
    
    ```bash
    ln -s libcrypto.so.1.1.0 libcrypto.so.2
    ```
    
#### 配置 Nginx 访问

在上一步安装成功之后，就可以通过配置 `https://<your-domain-name>.com` 进行访问了，生成的默认账户密码如下：

- 账户：root
- 密码：使用 `cat /etc/gitlab/initial_root_password` 命令查看

> 对于域名访问的说明： 
>
> 这里是通过 hosts 文件绑定进行访问的，这里配置的 Nginx 指的是配置外网访问。
> 如果 GitLab 服务器和代理服务器在一台服务器上，可以直接进行 DNS 解析，这里 GitLab 所在服务器和代理服务器是两台逻辑上独立的机器。需要在代理服务器上配置解析以访问应用服务器上的 GitLab 服务。

1. 配置 GitLab 的监听端口

    ```bash
    vim /etc/gitlab/gitlab.rc
    ```
    
    取消如下配置的注释内容：
    
    ```diff
    -  # nginx['listen_port'] = 9000
   +  nginx['listen_port'] = 9000
    ```
    
2. 重新读取 GitLab 配置

    ```bash
    gitlab-ctl reconfigure
    ```
    
3. 在代理服务器上添加 Nginx 配置

    ```bash
    vim /etc/nginc/conf/conf.d/gitlab.conf
    ```

    写入如下内容：
    

    ```bash
    server {
        listen 443;
        server_name gitlab.<your-domain-name>.com;

        ssl on;
        ssl_certificate /etc/nginx/ca/<your-domain-name>.com.cer;
        ssl_certificate_key /etc/nginx/ca/<your-domain-name>.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
        ssl_prefer_server_ciphers on;

        location / {
            proxy_pass https://<gitlab-server-ip>:9000;
            proxy_redirect off;
            proxy_headers_hash_max_size 51200;
            proxy_headers_hash_bucket_size 6400;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
    ```
    
4. 重载 Nginx

    ```bash
    nginx -t
    nginx -s reload
    ```
    
### Kubernetes 安装

## GitLab 使用教程

### 数据备份和恢复