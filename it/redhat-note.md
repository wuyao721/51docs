# devtoolset
redhat系统开发工具(GCC、G++)升级速度很忙，有时导致无法编译新的项目。手工升级工具链又很麻烦，redhat提供了devtoolset工具集方便快速切换工具链。

## 安装scl命令
```
yum install scl-utils
```

### 手工安装
https://www.softwarecollections.org/en/scls/rhscl/devtoolset-7/
http://mirror.centos.org/centos/7/sclo/x86_64/rh/Packages/d/

### yum安装
```
yum install centos-release-scl-rh
yum install devtoolset-7-gcc devtoolset-7-gcc-c++
```

## 公司CVM、docker环境下的 命令
yum install centos-release-scl
yum install devtoolset-7 --setopt=obsoletes=0

## 启动
scl enable devtoolset-7 bash

# 其他
1. 命令setup
2. ntsysv
3. /etc/sysconfig/
4. setup 设置IP后要修改配置文件，让其自动起作用
