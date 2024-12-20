# [RQ-001] Jenkins 问题集合

## Jenkins 下载插件速度慢

### 问题描述

安装完 Jenkins 后，Jenkins 下载插件速度缓慢。

### 原因分析

安装好 Jenkins 后，Jenkins 需要的文件目录已经生成完毕了，`default.json`是下载安装插件时调用的配置文件，其中的地址还是`update.jenkins.io`，我们需要修改为国内源。

###  解决办法

修改 `default.json` 文件内的源为国内源：

```bash
sed -i 's#updates.jenkins.io/download/plugins#mirrors.tuna.tsinghua.edu.cn/jenkins/plugins#g' default.json && sed -i 's#www.google.com#www.baidu.com#g' default.json
```

修改完成后，重启 Jenkins

```bash
systemctl restart jenkins
```