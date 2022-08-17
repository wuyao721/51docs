# 华为交换机常用命令

- 进入修改模式 sy

- 查看配置
```
display current-configuration
display current-configuration interface 10GE 1/0/21
```

- 清除网口配置 
```
clear configuration interface 10GE 1/0/21
```

- 配置网口
```
interface 10GE 1/0/20
undo port hybrid vlan 1
port link-type hybrid
port hybrid pvid vlan 120
port hybrid untagged vlan 121
```

- 显示网口流量
```
display interface counters 10GE 1/0/20
display interface counters rate 10GE 1/0/20
```

- 提交修改
```
commit
```

# 思科交换机常用命令

- 进入特权模式
```
enable
```

- 进入配置模式
```
config
```

- 查看各个网卡网速
```
show interfaces counters rate
```

- 查看所有的vlan
```
show vlan 
```

- 查看所有网卡状态
```
show interfaces status 
```
