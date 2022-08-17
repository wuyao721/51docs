
# 证书登陆cvm或者docker
1. 生成证书ssh-kengen
2. 将~/.ssh/id_rsa.pub的内容拷贝到cvm的~/.ssh/authorized_keys文件中
3. 配置ssh命令参数，不用每次都手工输入
```
vi ~/.ssh/config
Host 1.1.1.44
    HostName 1.1.1.44
    User root
    Port 22
Host p44
    HostName p44
    User root
    Port 22
```
4. 修改/etc/hosts，添加别名
4. 登陆测试
ssh p44

# 配置git环境
1. 设置证书，域名以及登陆用户名
```
~/.ssh/config
Host git.xxx.com
    HostName git.xxx.com
    User xx@compary.com
    Port 22
    IdentityFile ~/.ssh/xxx_rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
Host git.code.xxx.com
    HostName git.code.xxx.com
    User xx@compary.com
    Port 22
    IdentityFile ~/.ssh/xxx_rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
```

2. 设置git用户名称，并将https转为git
# .gitconfig
```
[url "git@git.xxx.com:"]
    insteadOf = "https://git.code.compary.com/"
[url "git@git.xxx.com:"]
    insteadOf = "http://git.code.compary.com/"
[url "git@git.xxx.com:"]
    insteadOf = "https://git.compary.com/"
[url "git@git.xxx.com:"]
    insteadOf = "http://git.compary.com/"
[user]
	name = xxx
	email = xx@compary.com
```

