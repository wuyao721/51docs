# <center> DPDK学习笔记

# 搭建DPDk开发环境

## 编译源码
1. rpm -ivh numactl-devel-2.0.9-6.el7_2.x86_64.rpm
2. 解压安装包
4. 运行usertools/dpdk-setup.sh，选14编译
5. 选17，安装驱动
6. 选21，设置大页
7. 选23，绑端口
8. 退出，修改/etc/.bash_profile,在PATH下加上：
``` bash
export RTE_SDK=/home/dpdk-stable-18.02.2
export RTE_TARGET=x86_64-native-linuxapp-gcc
```
9. 保存退出，重启一个shell


## 配置
1. 修改GRUB，配置大页及CPU核
		1. vi /etc/default/grub
			在GRUB_CMDLINE_LINUX这个配置项最后添加：
			default_hugepagesz=1G hugepagesz=1G hugepages=8 isolcpus=1-7
			
		2. grub2-mkconfig -o /boot/grub2/grub.cfg
		3. reboot

2. 加载驱动
		1. /sbin/insmod $RTE_SDK/$RTE_TARGET/kmod/igb_uio.ko
		
3. 绑定端口
		1. sudo ${RTE_SDK}/usertools/dpdk-devbind.py -b igb_uio $PCI_PATH && echo "OK"
		其中$PCI_PATH输入的端口PCI通道
		
4. 启动程序


# pktgen

同时一起收发两个口
```
./app/x86_64-native-linuxapp-gcc/pktgen -l 0-4 -n 4 -- -p 0x3 -P -s 0:/home/460024066300112_sample.pcap -s 1:/home/460024066300112_sample.pcap -m "[1:2].0,[3:4].1"
```

同时分开发两个口
```
./app/x86_64-native-linuxapp-gcc/pktgen -l 0-2 -n 4 --file-prefix=port0 --socket-mem=1024 -- -p 0x1 -P -s 0:/home/460024066300112_sample.pcap -m "[1:2].0"
./app/x86_64-native-linuxapp-gcc/pktgen -l 3-5 -n 4 --file-prefix=port1 --socket-mem=1024 -- -p 0x2 -P -s 1:/home/460024066300112_sample.pcap -m "[4:5].1"

./app/x86_64-native-linuxapp-gcc/pktgen -l 0,2,4 -n 4 -- -p 0x1 -P -s 0:/home/460024066300112_sample.pcap -m "[2:4].0"
```


# 大页内存
- 查看大页内存的使用情况：
```
[root@twomoon hugepage]# grep Huge /proc/meminfo 
AnonHugePages:    172032 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
```

HugePages_Total 是指系统总共预留了多少 HugePages. HugePages_Free 指当前还有多少 HugePages 未分配 (allocate)，HugePages_Rsvd 是指有多少 HugePages 是系统承诺了会分配给程序(commitment to allocate)，但实际并未分配。HugePages_Surp 指超分的页。

- 查看的大页使用情况：
```
[root@twomoon ~]$ ls /sys/kernel/mm/hugepages/
hugepages-1048576kB
[root@twomoon ~]$ ls /sys/kernel/mm/hugepages/hugepages-1048576kB/
free_hugepages           nr_hugepages             nr_hugepages_mempolicy   nr_overcommit_hugepages  resv_hugepages           surplus_hugepages        
[root@twomoon ~]$ ls /sys/kernel/mm/hugepages/hugepages-1048576kB/
free_hugepages  nr_hugepages  nr_hugepages_mempolicy  nr_overcommit_hugepages  resv_hugepages  surplus_hugepages
```

- 查看NUMA架构的大页使用情况：
```
[root@twomoon hugepage]# cat /sys/devices/system/node/node*/meminfo | fgrep Huge
Node 0 AnonHugePages:    172032 kB
Node 0 HugePages_Total:     0
Node 0 HugePages_Free:      0
Node 0 HugePages_Surp:      0
```

- 修改大页内存的数量：
```
[root@twomoon hugepage]# echo 4 > /proc/sys/vm/nr_hugepages
[root@twomoon hugepage]# cat /proc/meminfo |grep Huge
AnonHugePages:    172032 kB
HugePages_Total:       4
HugePages_Free:        4
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
```

- 修改大页内存的数量（方法二）：
```
[root@twomoon hugepage]# sysctl vm.nr_hugepages=6
vm.nr_hugepages = 6
[root@twomoon hugepage]# sysctl vm.nr_hugepages
vm.nr_hugepages = 6
```

- 加载大页内存：
```
[root@twomoon hugepage]# mount -t hugetlbfs hugetlbfs /mnt/huge
[root@twomoon hugepage]# grep hugetlbfs /proc/mounts 
hugetlbfs /mnt/huge hugetlbfs rw,seclabel,relatime 0 0
```

- 什么是大页内存？
操作系统在查找内存的时候以页为单位，默认情况下每页内存2K大小，查找时候需要查找页表，而页表也需要放在内存中，并缓存起来。在大内存的系统中，需要大量的页表来组织内存。通过使用大页内存，可以减少页表的量，这将减少页面缓存miss的概率，从而减少CPU访问内存的时间。大页一般有两种情况2M，或者1G。大页内存带来的性能提升在大量内存频繁访问的场景中尤其明显。

- 什么是透明大页？
透明大页是linux内核自动为应用程序分配的大页内存，它与普通的大页内存类似，能够有效降低访问页表时缓存miss的几率。不同的是透明大页是由操作系统控制的，应用程序无须知道，当透明大页不够用时，操作系统会使用2K的页；而普通的大页内存需要用户自行分配以及使用。/proc/meminfo文件里面的AnonHugePages记录透明大页的使用量。文件/sys/kernel/mm/transparent_hugepage/enabled记录了透明大页是否打开，可以通过修改该文件来开关透明大页的功能。

- 如何申请（使用）大页内存？
对于2M的大页来说，首先修改/proc/sys/vm/nr_hugepages申请需要的数量，然后mount大页内存，最后使用shmget或者mmap申请内存的时候打上大页的申请标记。

- 如何清空大页内存？
首先，所有申请大页内存的程序都需要在退出的时候把申请的内存给释放，然后umount大页内存，最后修改/proc/sys/vm/nr_hugepages值为零。
再查看/proc/meminfo检查大页内存是否真的释放掉。


# DPDK大页内存初始化步骤
1. 遍历/sys/kernel/mm/hugepages/目录，读取每个子目录，得到页大小，记为A
```
[root@VM-162-44-centos ~]# ls /sys/kernel/mm/hugepages/
hugepages-2048kB
```

2. 获取默认大页页大小，记为B
```
[root@VM-162-44-centos ~]# cat /proc/meminfo |grep Hugepagesize
Hugepagesize:       2048 kB
```

3. 读取/mnt/mount，找到大页内存挂载目录，如下：
hugetlbfs /dev/hugepages hugetlbfs rw,relatime 0 0
读取该挂载点的页大小，记为C；如果该挂载点没有指定页大小，则认为该挂载点使用了默认大小B，记为C
比较A与C是否相等，如果相等，取挂载点目录，记为D

4. 打开挂载目录D，并上写锁。进入目录，尝试清理目录下的残留文件

5. 计算该挂载目录下剩下多少内存
读取/sys/kernel/mm/hugepages/hugepages-2048kB/free_hugepages
读取/sys/kernel/mm/hugepages/hugepages-2048kB/resv_hugepages
free_hugepages - resv_hugepages 得到可用的大页内存页数量
