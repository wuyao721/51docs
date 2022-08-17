# <center> Mellanox 网卡使用笔记

1. 接上网卡

1.1 查看插卡接入状态
```
[root@localhost ~]# lspci |grep Eth
01:00.0 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE SFP+ (rev 02)
01:00.1 Ethernet controller: Intel Corporation Ethernet Controller X710 for 10GbE SFP+ (rev 02)
81:00.0 Ethernet controller: Mellanox Technologies MT27800 Family [ConnectX-5]
81:00.1 Ethernet controller: Mellanox Technologies MT27800 Family [ConnectX-5]
c6:00.0 Ethernet controller: Broadcom Inc. and subsidiaries BCM57416 NetXtreme-E Dual-Media 10G RDMA Ethernet Controller (rev 01)
c6:00.1 Ethernet controller: Broadcom Inc. and subsidiaries BCM57416 NetXtreme-E Dual-Media 10G RDMA Ethernet Controller (rev 01)
```

1.2 查看网卡亲和性
```
[root@localhost ~]# hwloc-ls
Machine (126GB total)
  Package L#0
    NUMANode L#0 (P#0 63GB)
      L3 L#0 (16MB)
        L2 L#0 (512KB) + L1d L#0 (32KB) + L1i L#0 (32KB) + Core L#0 + PU L#0 (P#0)
        L2 L#1 (512KB) + L1d L#1 (32KB) + L1i L#1 (32KB) + Core L#1 + PU L#1 (P#1)
        L2 L#2 (512KB) + L1d L#2 (32KB) + L1i L#2 (32KB) + Core L#2 + PU L#2 (P#2)
        L2 L#3 (512KB) + L1d L#3 (32KB) + L1i L#3 (32KB) + Core L#3 + PU L#3 (P#3)
      L3 L#1 (16MB)
        L2 L#4 (512KB) + L1d L#4 (32KB) + L1i L#4 (32KB) + Core L#4 + PU L#4 (P#4)
        L2 L#5 (512KB) + L1d L#5 (32KB) + L1i L#5 (32KB) + Core L#5 + PU L#5 (P#5)
        L2 L#6 (512KB) + L1d L#6 (32KB) + L1i L#6 (32KB) + Core L#6 + PU L#6 (P#6)
        L2 L#7 (512KB) + L1d L#7 (32KB) + L1i L#7 (32KB) + Core L#7 + PU L#7 (P#7)
      L3 L#2 (16MB)
        L2 L#8 (512KB) + L1d L#8 (32KB) + L1i L#8 (32KB) + Core L#8 + PU L#8 (P#8)
        L2 L#9 (512KB) + L1d L#9 (32KB) + L1i L#9 (32KB) + Core L#9 + PU L#9 (P#9)
        L2 L#10 (512KB) + L1d L#10 (32KB) + L1i L#10 (32KB) + Core L#10 + PU L#10 (P#10)
        L2 L#11 (512KB) + L1d L#11 (32KB) + L1i L#11 (32KB) + Core L#11 + PU L#11 (P#11)
      L3 L#3 (16MB)
        L2 L#12 (512KB) + L1d L#12 (32KB) + L1i L#12 (32KB) + Core L#12 + PU L#12 (P#12)
        L2 L#13 (512KB) + L1d L#13 (32KB) + L1i L#13 (32KB) + Core L#13 + PU L#13 (P#13)
        L2 L#14 (512KB) + L1d L#14 (32KB) + L1i L#14 (32KB) + Core L#14 + PU L#14 (P#14)
        L2 L#15 (512KB) + L1d L#15 (32KB) + L1i L#15 (32KB) + Core L#15 + PU L#15 (P#15)
      L3 L#4 (16MB)
        L2 L#16 (512KB) + L1d L#16 (32KB) + L1i L#16 (32KB) + Core L#16 + PU L#16 (P#16)
        L2 L#17 (512KB) + L1d L#17 (32KB) + L1i L#17 (32KB) + Core L#17 + PU L#17 (P#17)
        L2 L#18 (512KB) + L1d L#18 (32KB) + L1i L#18 (32KB) + Core L#18 + PU L#18 (P#18)
        L2 L#19 (512KB) + L1d L#19 (32KB) + L1i L#19 (32KB) + Core L#19 + PU L#19 (P#19)
      L3 L#5 (16MB)
        L2 L#20 (512KB) + L1d L#20 (32KB) + L1i L#20 (32KB) + Core L#20 + PU L#20 (P#20)
        L2 L#21 (512KB) + L1d L#21 (32KB) + L1i L#21 (32KB) + Core L#21 + PU L#21 (P#21)
        L2 L#22 (512KB) + L1d L#22 (32KB) + L1i L#22 (32KB) + Core L#22 + PU L#22 (P#22)
        L2 L#23 (512KB) + L1d L#23 (32KB) + L1i L#23 (32KB) + Core L#23 + PU L#23 (P#23)
      HostBridge L#0
        PCIBridge
          PCI 15b3:1017
            Net L#0 "enp129s0f0"
            OpenFabrics L#1 "mlx5_0"
          PCI 15b3:1017
            Net L#2 "enp129s0f1"
            OpenFabrics L#3 "mlx5_1"
      HostBridge L#2
        PCIBridge
          PCIBridge
            PCI 1a03:2000
              GPU L#4 "controlD64"
              GPU L#5 "card0"
        PCIBridge
          PCI 1b21:0612
        PCIBridge
          PCI 14e4:16d8
            Net L#6 "eno1np0"
          PCI 14e4:16d8
            Net L#7 "eno2np1"
    NUMANode L#1 (P#1 63GB)
      L3 L#6 (16MB)
        L2 L#24 (512KB) + L1d L#24 (32KB) + L1i L#24 (32KB) + Core L#24 + PU L#24 (P#24)
        L2 L#25 (512KB) + L1d L#25 (32KB) + L1i L#25 (32KB) + Core L#25 + PU L#25 (P#25)
        L2 L#26 (512KB) + L1d L#26 (32KB) + L1i L#26 (32KB) + Core L#26 + PU L#26 (P#26)
        L2 L#27 (512KB) + L1d L#27 (32KB) + L1i L#27 (32KB) + Core L#27 + PU L#27 (P#27)
      L3 L#7 (16MB)
        L2 L#28 (512KB) + L1d L#28 (32KB) + L1i L#28 (32KB) + Core L#28 + PU L#28 (P#28)
        L2 L#29 (512KB) + L1d L#29 (32KB) + L1i L#29 (32KB) + Core L#29 + PU L#29 (P#29)
        L2 L#30 (512KB) + L1d L#30 (32KB) + L1i L#30 (32KB) + Core L#30 + PU L#30 (P#30)
        L2 L#31 (512KB) + L1d L#31 (32KB) + L1i L#31 (32KB) + Core L#31 + PU L#31 (P#31)
      L3 L#8 (16MB)
        L2 L#32 (512KB) + L1d L#32 (32KB) + L1i L#32 (32KB) + Core L#32 + PU L#32 (P#32)
        L2 L#33 (512KB) + L1d L#33 (32KB) + L1i L#33 (32KB) + Core L#33 + PU L#33 (P#33)
        L2 L#34 (512KB) + L1d L#34 (32KB) + L1i L#34 (32KB) + Core L#34 + PU L#34 (P#34)
        L2 L#35 (512KB) + L1d L#35 (32KB) + L1i L#35 (32KB) + Core L#35 + PU L#35 (P#35)
      L3 L#9 (16MB)
        L2 L#36 (512KB) + L1d L#36 (32KB) + L1i L#36 (32KB) + Core L#36 + PU L#36 (P#36)
        L2 L#37 (512KB) + L1d L#37 (32KB) + L1i L#37 (32KB) + Core L#37 + PU L#37 (P#37)
        L2 L#38 (512KB) + L1d L#38 (32KB) + L1i L#38 (32KB) + Core L#38 + PU L#38 (P#38)
        L2 L#39 (512KB) + L1d L#39 (32KB) + L1i L#39 (32KB) + Core L#39 + PU L#39 (P#39)
      L3 L#10 (16MB)
        L2 L#40 (512KB) + L1d L#40 (32KB) + L1i L#40 (32KB) + Core L#40 + PU L#40 (P#40)
        L2 L#41 (512KB) + L1d L#41 (32KB) + L1i L#41 (32KB) + Core L#41 + PU L#41 (P#41)
        L2 L#42 (512KB) + L1d L#42 (32KB) + L1i L#42 (32KB) + Core L#42 + PU L#42 (P#42)
        L2 L#43 (512KB) + L1d L#43 (32KB) + L1i L#43 (32KB) + Core L#43 + PU L#43 (P#43)
      L3 L#11 (16MB)
        L2 L#44 (512KB) + L1d L#44 (32KB) + L1i L#44 (32KB) + Core L#44 + PU L#44 (P#44)
        L2 L#45 (512KB) + L1d L#45 (32KB) + L1i L#45 (32KB) + Core L#45 + PU L#45 (P#45)
        L2 L#46 (512KB) + L1d L#46 (32KB) + L1i L#46 (32KB) + Core L#46 + PU L#46 (P#46)
        L2 L#47 (512KB) + L1d L#47 (32KB) + L1i L#47 (32KB) + Core L#47 + PU L#47 (P#47)
      HostBridge L#7
        PCIBridge
          PCI 8086:1572
            Net L#8 "enp1s0f0"
          PCI 8086:1572
            Net L#9 "enp1s0f1"
      HostBridge L#9
        PCIBridge
          PCI 1022:7901
            Block(Disk) L#10 "sdb"
            Block(Disk) L#11 "sda"
        PCIBridge
          PCI 1022:7901
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
  Misc(MemoryModule)
```

2. 安装驱动
2.1 安装
```
[root@server ~]# ./mlnxofedinstall --dpdk --upstream-libs --enable-mlnx_tune --with-kernel-mft-mlnx --with-mft --with-mlnx-ethtool --force
```

2.2. 查看安装包版本：
```
[root@server ~]# ofed_info
MLNX_OFED_LINUX-5.0-2.1.8.0 (OFED-5.0-2.1.8):
ar_mgr:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/ar_mgr-1.0-0.49.MLNX20200216.g4ea049f.50100.0.src.rpm

cc_mgr:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/cc_mgr-1.0-0.48.MLNX20200216.g4ea049f.50100.0.src.rpm

dapl:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/dapl-2.1.10mlnx-OFED.3.4.2.1.0.50100.0.src.rpm

dump_pr:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/dump_pr-1.0-0.44.MLNX20200216.g4ea049f.50100.0.src.rpm

fabric-collector:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/fabric-collector-1.1.0.MLNX20170103.89bb2aa-0.1.50100.0.src.rpm

gpio-mlxbf:
mlnx_ofed_soc/gpio-mlxbf-1.0-0.g6d44a8a.src.rpm

hcoll:
mlnx_ofed_hcol/hcoll-4.5.3045-1.src.rpm

i2c-mlx:
mlnx_ofed_soc/i2c-mlx-1.0-0.g422740c.src.rpm

ibacm:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/ibacm-41mlnx1-OFED.4.3.3.0.0.50100.0.src.rpm

ibdump:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/ibdump-6.0.0-1.50100.0.src.rpm

ibsim:
mlnx_ofed_ibsim/ibsim-0.9.tar.gz

ibutils:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/ibutils-1.5.7.1-0.12.gdcaeae2.50100.0.src.rpm

ibutils2:
ibutils2/ibutils2-2.1.1-0.121.MLNX20200324.g061a520.tar.gz

infiniband-diags:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/infiniband-diags-5.6.0.MLNX20200211.354e4b7-0.1.50100.0.src.rpm

iser:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

isert:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

kernel-mft:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/kernel-mft-4.14.0-105.src.rpm

knem:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/knem-1.1.3.90mlnx1-OFED.5.0.0.3.8.1.g12569ca.src.rpm

libibcm:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/libibcm-41mlnx1-OFED.4.1.0.1.0.50100.0.src.rpm

libibmad:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/libibmad-5.4.0.MLNX20190423.1d917ae-0.1.50100.0.src.rpm

libibumad:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/libibumad-43.1.1.MLNX20200211.078947f-0.1.50100.0.src.rpm

libibverbs:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/libibverbs-41mlnx1-OFED.5.0.0.0.9.50100.0.src.rpm

libmlx4:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/libmlx4-41mlnx1-OFED.4.7.3.0.3.50100.0.src.rpm

libmlx5:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/libmlx5-41mlnx1-OFED.5.0.0.4.0.50100.0.src.rpm

libpka:
mlnx_ofed_soc/libpka-1.0-1.g6cc68a2.src.rpm

librdmacm:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/librdmacm-41mlnx1-OFED.4.7.3.0.6.50100.0.src.rpm

librxe:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/librxe-41mlnx1-OFED.4.4.2.4.6.50100.0.src.rpm

libvma:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/libvma-9.0.2-0.src.rpm

mlnx-dpdk:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/mlnx-dpdk-19.11.0-1.50100.0.src.rpm

mlnx-en:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

mlnx-ethtool:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/mlnx-ethtool-5.4-1.50100.0.src.rpm

mlnx-iproute2:
mlnx_ofed/iproute2.git mlnx_ofed_5_0_2
commit 766bdabfca4382539a642fc20e762d2461fd80d4
mlnx-nfsrdma:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

mlnx-nvme:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

mlnx-ofa_kernel:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

mlnx-rdma-rxe:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

mlx-bootctl:
mlnx_ofed_soc/mlx-bootctl-1.3-0.g2aa74b7.src.rpm

mlx-l3cache:
mlnx_ofed_soc/mlx-l3cache-0.1-1.gebb0728.src.rpm

mlx-pmc:
mlnx_ofed_soc/mlx-pmc-1.1-0.g1141c2e.src.rpm

mlx-trio:
mlnx_ofed_soc/mlx-trio-0.1-1.g9d13513.src.rpm

mlxbf-livefish:
mlnx_ofed_soc/mlxbf-livefish-1.0-0.gec08328.src.rpm

mpi-selector:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/mpi-selector-1.0.3-1.50100.0.src.rpm

mpitests:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/mpitests-3.2.20-e1a0676.50100.0.src.rpm

mstflint:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/mstflint-4.13.0-1.41.g4e8819c.50100.0.src.rpm

multiperf:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/multiperf-3.0-0.14.g5f0fd0e.50100.0.src.rpm

multiperf:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/multiperf-3.0.0.mlnxlibs-0.13.gcdaa426.50100.0.src.rpm

mxm:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/mxm-3.7.3112-1.50100.0.src.rpm

nvme-snap:
nvme/nvme-snap-2.1.0-126.mlnx.src.rpm

ofed-docs:
docs.git mlnx_ofed-4.0
commit 3d1b0afb7bc190ae5f362223043f76b2b45971cc

openmpi:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/openmpi-4.0.3rc4-1.50100.0.src.rpm

opensm:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/opensm-5.6.0.MLNX20200217.cedc1e4-0.1.50100.0.src.rpm

openvswitch:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/openvswitch-2.12.1-1.50100.0.src.rpm

perftest:
mlnx_ofed_perftest/perftest-4.4-0.23.g89e176a.tar.gz

perftest:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/perftest-4.4.0.mlnxlibs-0.11.gd240b65.50100.0.src.rpm

pka-mlxbf:
mlnx_ofed_soc/pka-mlxbf-1.0-0.g963f663.src.rpm

qperf:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/qperf-0.4.11-1.50100.0.src.rpm

rdma-core:
mlnx_ofed/rdma-core.git mlnx_ofed_5_0
commit 65c61bb46cb8829836b10e31a50b7d1a26bed300
rshim:
mlnx_ofed_soc/rshim-1.18-0.gb99e894.src.rpm

sharp:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/sharp-2.1.0.MLNX20200223.f63394a9c8-1.50100.0.src.rpm

sockperf:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/sockperf-3.7-0.gita1e8e835a689.50100.0.src.rpm

srp:
mlnx_ofed/mlnx-ofa_kernel-4.0.git mlnx_ofed_5_0_2
commit 5f671787b15915c18332662fc857a2decbc0be0d

srptools:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/srptools-41mlnx1-5.50100.0.src.rpm

tmfifo:
mlnx_ofed_soc/tmfifo-1.5-0.g31e8a6e.src.rpm

ucx:
/sw/release/mlnx_ofed/IBHPC/MLNX_OFED_LINUX-5.0-1.0.0.0/SRPMS/ucx-1.8.0-1.50100.0.src.rpm


Installed Packages:
-------------------

kmod-mlnx-ofa_kernel
ibacm
rdma-core-devel
mlnx-ofa_kernel
librdmacm
mstflint
mlnx-ethtool
librdmacm-utils
mlnx-ofa_kernel-devel
infiniband-diags
kmod-kernel-mft-mlnx
libibverbs
mlnx-iproute2
libibverbs-utils
libibumad
rdma-core
```

2.3. 查看固件版本：
```
[root@server ~]# flint -d 41:00.0 q
Image type:            FS4
FW Version:            22.27.1016
FW Release Date:       27.2.2020
Product Version:       22.27.1016
Rom Info:              type=UEFI version=14.20.19 cpu=AMD64,AARCH64
                       type=PXE version=3.5.901 cpu=AMD64
Description:           UID                GuidsNumber
Base GUID:             b8599f0300f08018        4
Base MAC:              b8599ff08018            4
Image VSD:             N/A
Device VSD:            N/A
PSID:                  MT_0000000359
Security Attributes:   N/A

MLNX_OFED_LINUX-4.7-3.2.9.0-rhelx.x-x86_64.tgz固件版本：
Image type:            FS4
FW Version:            16.26.4012
FW Release Date:       10.12.2019
Product Version:       16.26.4012
Rom Info:              type=UEFI version=14.19.17 cpu=AMD64
                       type=PXE version=3.5.805 cpu=AMD64
Description:           UID                GuidsNumber
Base GUID:             b8599f03001d06c2        8
Base MAC:              b8599f1d06c2            8
Image VSD:             N/A
Device VSD:            N/A
PSID:                  MT_0000000012
Security Attributes:   N/A
```

2.4 手工升级固件刷新固件：
```
[root@server ~]# unzip fw-ConnectX6Dx-rel-22_27_1016-MCX623106AN-CDA_Ax-UEFI-14.20.19-FlexBoot-3.5.901.bin.zip
[root@server ~]# flint -d /dev/mst/mt4125_pciconf0 -i fw-ConnectX6Dx-rel-22_27_1016-MCX623106AN-CDA_Ax-UEFI-14.20.19-FlexBoot-3.5.901.bin b
[root@server ~]# mlxfwreset -d /dev/mst/mt4125_pciconf0
```

3. 系统自带的工具观察设备
3.1 使用dmesg查看内核模块日志（如果有异常可从这里看到）
```
[root@server ~]# dmesg
[   13.052081] IPv6: ADDRCONF(NETDEV_UP): eno2np1: link is not ready
[   13.381462] IPv6: ADDRCONF(NETDEV_UP): eno2np1: link is not ready
[   13.384553] IPv6: ADDRCONF(NETDEV_UP): enp1s0f0: link is not ready
[   13.740127] mlx5_core 0000:01:00.0 enp1s0f0: Link down
[   13.750978] IPv6: ADDRCONF(NETDEV_UP): enp1s0f0: link is not ready
[   13.756498] IPv6: ADDRCONF(NETDEV_UP): enp1s0f1: link is not ready
[   14.092298] mlx5_core 0000:01:00.1 enp1s0f1: Link up
[   14.102763] IPv6: ADDRCONF(NETDEV_UP): enp1s0f1: link is not ready
[   14.112046] IPv6: ADDRCONF(NETDEV_CHANGE): enp1s0f1: link becomes ready
[   14.112935] IPv6: ADDRCONF(NETDEV_UP): enp65s0f0: link is not ready
[   16.409758] mlx5_core 0000:41:00.0 enp65s0f0: Link down
[   16.437363] IPv6: ADDRCONF(NETDEV_UP): enp65s0f0: link is not ready
[   16.447764] IPv6: ADDRCONF(NETDEV_UP): enp65s0f1: link is not ready
[   18.753532] mlx5_core 0000:41:00.1 enp65s0f1: Link up
[   18.775032] IPv6: ADDRCONF(NETDEV_UP): enp65s0f1: link is not ready
[   18.780940] IPv6: ADDRCONF(NETDEV_CHANGE): enp65s0f1: link becomes ready
[   18.801031] IPv6: ADDRCONF(NETDEV_UP): eno2np1: link is not ready
[   18.802045] IPv6: ADDRCONF(NETDEV_UP): enp1s0f0: link is not ready
[   18.803499] IPv6: ADDRCONF(NETDEV_UP): enp65s0f0: link is not ready
[   22.810568] igb_uio: Use MSIX interrupt by default
[   51.227747] mlx5_core 0000:41:00.1: temp_warn:170:(pid 0): High temperature on sensors with bit set 0 1
[   51.227791] mlx5_core 0000:41:00.0: temp_warn:170:(pid 0): High temperature on sensors with bit set 0 1
[  131.207015] mlx5_core 0000:41:00.0: poll_health:471:(pid 0): device's health compromised - reached miss count
[  131.207025] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[0] 0x00000073
[  131.207029] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[1] 0x00000073
[  131.207032] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[2] 0x00000000
[  131.207036] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[3] 0x00000000
[  131.207039] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[4] 0x00000000
[  131.207042] mlx5_core 0000:41:00.0: print_health_info:413:(pid 0): assert_exit_ptr 0x20a36f94
[  131.207046] mlx5_core 0000:41:00.0: print_health_info:415:(pid 0): assert_callra 0x20a0e5b4
[  131.207053] mlx5_core 0000:41:00.0: print_health_info:417:(pid 0): fw_ver 22.99.7864
[  131.207056] mlx5_core 0000:41:00.0: print_health_info:418:(pid 0): hw_id 0x00000212
[  131.207059] mlx5_core 0000:41:00.0: print_health_info:419:(pid 0): irisc_index 0
[  131.207065] mlx5_core 0000:41:00.0: print_health_info:421:(pid 0): synd 0x10: High temperature
[  131.207068] mlx5_core 0000:41:00.0: print_health_info:422:(pid 0): ext_synd 0x0000
[  131.207071] mlx5_core 0000:41:00.0: print_health_info:424:(pid 0): raw fw_ver 0x16631eb8
[  131.239013] mlx5_core 0000:41:00.1: poll_health:471:(pid 0): device's health compromised - reached miss count
[  131.239019] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[0] 0x00000073
[  131.239023] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[1] 0x00000073
[  131.239026] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[2] 0x00000000
[  131.239030] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[3] 0x00000000
[  131.239033] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[4] 0x00000000
[  131.239036] mlx5_core 0000:41:00.1: print_health_info:413:(pid 0): assert_exit_ptr 0x20a36f94
[  131.239039] mlx5_core 0000:41:00.1: print_health_info:415:(pid 0): assert_callra 0x20a0e5b4
[  131.239045] mlx5_core 0000:41:00.1: print_health_info:417:(pid 0): fw_ver 22.99.7864
[  131.239049] mlx5_core 0000:41:00.1: print_health_info:418:(pid 0): hw_id 0x00000212
[  131.239052] mlx5_core 0000:41:00.1: print_health_info:419:(pid 0): irisc_index 0
[  131.239056] mlx5_core 0000:41:00.1: print_health_info:421:(pid 0): synd 0x10: High temperature
[  131.239060] mlx5_core 0000:41:00.1: print_health_info:422:(pid 0): ext_synd 0x0000
[  131.239063] mlx5_core 0000:41:00.1: print_health_info:424:(pid 0): raw fw_ver 0x16631eb8
[  204.791017] mlx5_core 0000:41:00.0: poll_health:478:(pid 0): Fatal error 1 detected
[  204.791023] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[0] 0xffffffff
[  204.791026] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[1] 0xffffffff
[  204.791028] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[2] 0xffffffff
[  204.791029] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[3] 0xffffffff
[  204.791031] mlx5_core 0000:41:00.0: print_health_info:410:(pid 0): assert_var[4] 0xffffffff
[  204.791033] mlx5_core 0000:41:00.0: print_health_info:413:(pid 0): assert_exit_ptr 0xffffffff
[  204.791035] mlx5_core 0000:41:00.0: print_health_info:415:(pid 0): assert_callra 0xffffffff
[  204.791038] mlx5_core 0000:41:00.0: print_health_info:417:(pid 0): fw_ver 65535.65535.65535
[  204.791040] mlx5_core 0000:41:00.0: print_health_info:418:(pid 0): hw_id 0xffffffff
[  204.791042] mlx5_core 0000:41:00.0: print_health_info:419:(pid 0): irisc_index 255
[  204.791044] mlx5_core 0000:41:00.0: print_health_info:421:(pid 0): synd 0xff: unrecognized error
[  204.791046] mlx5_core 0000:41:00.0: print_health_info:422:(pid 0): ext_synd 0xffff
[  204.791048] mlx5_core 0000:41:00.0: print_health_info:424:(pid 0): raw fw_ver 0xffffffff
[  204.791060] mlx5_core 0000:41:00.0: health_care:350:(pid 920): handling bad device here
[  204.791063] mlx5_core 0000:41:00.0: mlx5_handle_bad_state:274:(pid 920): NIC mode: 7
[  204.791064] mlx5_core 0000:41:00.0: mlx5_handle_bad_state:300:(pid 920): Expected to see disabled NIC but it is has invalid value 7
[  204.791065] mlx5_core 0000:41:00.0: mlx5_pci_err_detected was called
[  204.791067] mlx5_core 0000:41:00.0: mlx5_enter_error_state:214:(pid 920): start
[  204.791086] mlx5_core 0000:41:00.0: mlx5_pcie_event:293:(pid 918): PCIe slot power capability was not advertised.
[  204.823015] mlx5_core 0000:41:00.1: poll_health:478:(pid 0): Fatal error 1 detected
[  204.823018] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[0] 0xffffffff
[  204.823020] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[1] 0xffffffff
[  204.823022] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[2] 0xffffffff
[  204.823024] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[3] 0xffffffff
[  204.823026] mlx5_core 0000:41:00.1: print_health_info:410:(pid 0): assert_var[4] 0xffffffff
[  204.823028] mlx5_core 0000:41:00.1: print_health_info:413:(pid 0): assert_exit_ptr 0xffffffff
[  204.823030] mlx5_core 0000:41:00.1: print_health_info:415:(pid 0): assert_callra 0xffffffff
[  204.823033] mlx5_core 0000:41:00.1: print_health_info:417:(pid 0): fw_ver 65535.65535.65535
[  204.823035] mlx5_core 0000:41:00.1: print_health_info:418:(pid 0): hw_id 0xffffffff
[  204.823037] mlx5_core 0000:41:00.1: print_health_info:419:(pid 0): irisc_index 255
[  204.823039] mlx5_core 0000:41:00.1: print_health_info:421:(pid 0): synd 0xff: unrecognized error
[  204.823041] mlx5_core 0000:41:00.1: print_health_info:422:(pid 0): ext_synd 0xffff
[  204.823043] mlx5_core 0000:41:00.1: print_health_info:424:(pid 0): raw fw_ver 0xffffffff
[  204.823050] mlx5_core 0000:41:00.1: health_care:350:(pid 917): handling bad device here
[  204.823052] mlx5_core 0000:41:00.1: mlx5_handle_bad_state:274:(pid 917): NIC mode: 7
[  204.823054] mlx5_core 0000:41:00.1: mlx5_handle_bad_state:300:(pid 917): Expected to see disabled NIC but it is has invalid value 7
[  204.823055] mlx5_core 0000:41:00.1: mlx5_pci_err_detected was called
[  204.823057] mlx5_core 0000:41:00.1: mlx5_enter_error_state:214:(pid 917): start
[  204.823074] mlx5_core 0000:41:00.1: mlx5_pcie_event:293:(pid 410): PCIe slot power capability was not advertised.
[  205.792018] mlx5_core 0000:41:00.0: NIC IFC still 7 after 1000ms.
[  205.792021] mlx5_core 0000:41:00.0: mlx5_enter_error_state:264:(pid 920): end
[  205.824019] mlx5_core 0000:41:00.1: NIC IFC still 7 after 1000ms.
[  205.824022] mlx5_core 0000:41:00.1: mlx5_enter_error_state:264:(pid 917): end
[  206.171040] mlx5_core 0000:41:00.0: mlx5_wait_for_pages:711:(pid 920): Skipping wait for vf pages stage
[  206.171081] mlx5_core 0000:41:00.1: mlx5_wait_for_pages:711:(pid 917): Skipping wait for vf pages stage
[  207.895305] mlx5_core 0000:41:00.0: health_care:357:(pid 920): Scheduling recovery work with 60000ms delay
[  207.903150] mlx5_core 0000:41:00.1: health_care:357:(pid 917): Scheduling recovery work with 60000ms delay
[  271.327020] mlx5_core 0000:41:00.1: health recovery flow aborted, PCI reads still not working
[  271.327022] mlx5_core 0000:41:00.0: health recovery flow aborted, PCI reads still not working
[  597.036144] fuse init (API version 7.27)
```

3.2 lspci
查看接口信息lspci -s 41:00.0 -vv，可以在这里看到PCIE的速率
```
[root@server ~]# lspci -s 41:00.0 -t
[root@server ~]# lspci -s 40:03.1 -t
[root@server ~]# lspci -s 41:00.0 -vv
```

3.3. 查看是否连接上：
```
[root@server ~]# ethtool enp65s0f0
Settings for enp65s0f0:
	Supported ports: [ ]
	Supported link modes:   1000baseT/Full
	                        1000baseKX/Full
	                        10000baseT/Full
	                        10000baseKR/Full
	                        40000baseKR4/Full
	                        40000baseCR4/Full
	                        40000baseSR4/Full
	                        40000baseLR4/Full
	                        25000baseCR/Full
	                        25000baseKR/Full
	                        25000baseSR/Full
	                        50000baseCR2/Full
	                        50000baseKR2/Full
	                        100000baseKR4/Full
	                        100000baseSR4/Full
	                        100000baseCR4/Full
	                        100000baseLR4_ER4/Full
	                        50000baseSR2/Full
	                        1000baseX/Full
	                        10000baseCR/Full
	                        10000baseSR/Full
	                        10000baseLR/Full
	                        10000baseER/Full
	                        50000baseKR/Full
	                        50000baseSR/Full
	                        50000baseCR/Full
	                        50000baseLR_ER_FR/Full
	                        50000baseDR/Full
	                        100000baseKR2/Full
	                        100000baseSR2/Full
	                        100000baseCR2/Full
	                        100000baseLR2_ER2_FR2/Full
	                        100000baseDR2/Full
	Supported pause frame use: Symmetric
	Supports auto-negotiation: Yes
	Supported FEC modes: Not reported
	Advertised link modes:  1000baseT/Full
	                        1000baseKX/Full
	                        10000baseT/Full
	                        10000baseKR/Full
	                        40000baseKR4/Full
	                        40000baseCR4/Full
	                        40000baseSR4/Full
	                        40000baseLR4/Full
	                        25000baseCR/Full
	                        25000baseKR/Full
	                        25000baseSR/Full
	                        50000baseCR2/Full
	                        50000baseKR2/Full
	                        100000baseKR4/Full
	                        100000baseSR4/Full
	                        100000baseCR4/Full
	                        100000baseLR4_ER4/Full
	                        50000baseSR2/Full
	                        1000baseX/Full
	                        10000baseCR/Full
	                        10000baseSR/Full
	                        10000baseLR/Full
	                        10000baseER/Full
	                        50000baseKR/Full
	                        50000baseSR/Full
	                        50000baseCR/Full
	                        50000baseLR_ER_FR/Full
	                        50000baseDR/Full
	                        100000baseKR2/Full
	                        100000baseSR2/Full
	                        100000baseCR2/Full
	                        100000baseLR2_ER2_FR2/Full
	                        100000baseDR2/Full
	Advertised pause frame use: Symmetric
	Advertised auto-negotiation: Yes
	Advertised FEC modes: Not reported
	Speed: Unknown!
	Duplex: Unknown! (255)
	Port: Other
	PHYAD: 0
	Transceiver: internal
	Auto-negotiation: on
	Supports Wake-on: d
	Wake-on: d
	Current message level: 0x00000004 (4)
			       link
	Link detected: no
```

接上线以后是这样的：
```
[root@server ~]# ethtool enp65s0f0
Settings for enp65s0f0:
	Supported ports: [ FIBRE ]
	Supported link modes:   1000baseT/Full
	                        1000baseKX/Full
	                        10000baseT/Full
	                        10000baseKR/Full
	                        40000baseKR4/Full
	                        40000baseCR4/Full
	                        40000baseSR4/Full
	                        40000baseLR4/Full
	                        25000baseCR/Full
	                        25000baseKR/Full
	                        25000baseSR/Full
	                        50000baseCR2/Full
	                        50000baseKR2/Full
	                        100000baseKR4/Full
	                        100000baseSR4/Full
	                        100000baseCR4/Full
	                        100000baseLR4_ER4/Full
	                        50000baseSR2/Full
	                        1000baseX/Full
	                        10000baseCR/Full
	                        10000baseSR/Full
	                        10000baseLR/Full
	                        10000baseER/Full
	                        50000baseKR/Full
	                        50000baseSR/Full
	                        50000baseCR/Full
	                        50000baseLR_ER_FR/Full
	                        50000baseDR/Full
	                        100000baseKR2/Full
	                        100000baseSR2/Full
	                        100000baseCR2/Full
	                        100000baseLR2_ER2_FR2/Full
	                        100000baseDR2/Full
	Supported pause frame use: Symmetric
	Supports auto-negotiation: Yes
	Supported FEC modes: None RS
	Advertised link modes:  1000baseT/Full
	                        1000baseKX/Full
	                        10000baseT/Full
	                        10000baseKR/Full
	                        40000baseKR4/Full
	                        40000baseCR4/Full
	                        40000baseSR4/Full
	                        40000baseLR4/Full
	                        25000baseCR/Full
	                        25000baseKR/Full
	                        25000baseSR/Full
	                        50000baseCR2/Full
	                        50000baseKR2/Full
	                        100000baseKR4/Full
	                        100000baseSR4/Full
	                        100000baseCR4/Full
	                        100000baseLR4_ER4/Full
	                        50000baseSR2/Full
	                        1000baseX/Full
	                        10000baseCR/Full
	                        10000baseSR/Full
	                        10000baseLR/Full
	                        10000baseER/Full
	                        50000baseKR/Full
	                        50000baseSR/Full
	                        50000baseCR/Full
	                        50000baseLR_ER_FR/Full
	                        50000baseDR/Full
	                        100000baseKR2/Full
	                        100000baseSR2/Full
	                        100000baseCR2/Full
	                        100000baseLR2_ER2_FR2/Full
	                        100000baseDR2/Full
	Advertised pause frame use: No
	Advertised auto-negotiation: Yes
	Advertised FEC modes: RS
	Link partner advertised link modes:  Not reported
	Link partner advertised pause frame use: No
	Link partner advertised auto-negotiation: Yes
	Link partner advertised FEC modes: Not reported
	Speed: 100000Mb/s
	Duplex: Full
	Port: FIBRE
	PHYAD: 0
	Transceiver: internal
	Auto-negotiation: on
	Supports Wake-on: d
	Wake-on: d
	Current message level: 0x00000004 (4)
			       link
	Link detected: yes
```

3.4 关闭PAUSE帧处理
```
[root@AMD-107 ~]# ethtool -A enp65s0f0 rx off tx off
```

4. 使用Mellanox提供的工具
4.1 . 查看温度：
```
[root@server ~]# mget_temp -d /dev/mst/mt4125_pciconf0
88
```

88度明显高了，在BIOS中检查风扇是否正常，70度以下比较正常

4.2 查看连接状态（是否支持PCIE 4，是否有错报等信息）：
```
[root@server ~]# mlxlink -d 41:00.0 --port_type PCIE -e -c

PCIe Operational (Enabled) Info
-------------------------------
Depth, pcie index, node         : 0, 0, 0
Link Speed Active (Enabled)     : 16G-Gen 4 (16G-Gen 4)
Link Width Active (Enabled)     : 16X (16X)
Device Status                   : Correctable Error detected, Unsupported Request detected.

Management PCIe Timers Counters Info
------------------------------------
dl down                         : 3

Management PCIe Performance Counters Info
-----------------------------------------
RX Errors                       : 0
TX Errors                       : 9
CRC Error dllp                  : 0
CRC Error tlp                   : 0

EYE Opening Info (PCIe)
-----------------------
Physical Grade                  :  33626, 35074, 32382, 35680, 27787, 31053, 30168, 32200, 35607, 36040, 32870, 33005, 31672, 28427, 28630, 31043
Height Eye Opening [mV]         :    158,   167,   151,   163,   127,   147,   144,   154,   162,   166,   155,   154,   145,   131,   134,   145
Phase  Eye Opening [psec]       :     34,    32,    34,    34,    34,    32,    32,    32,    34,    34,    32,    34,    34,    34,    34,34
```

4.3 查看软件信息
```
[root@server ~]# mst status
MST modules:
------------
    MST PCI module is not loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4125_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:41:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
```

4.4 查看CEQ压缩是否打开
```
[root@AMD-107 ~]# mstconfig  q|grep CQE
         CQE_COMPRESSION                     AGGRESSIVE(1)
mt4125_pciconf0    mt4125_pciconf0.1
```
如果没有打开，就得重新打开
```
[root@AMD-107 ~]# mlxconfig -d /dev/mst/mt4125_pciconf0 set CQE_COMPRESSION=1


Device #1:
----------

Device type:    ConnectX6DX
Name:           MCX623106AN-CDA_Ax
Description:    ConnectX-6 Dx EN adapter card; 100GbE; Dual-port QSFP56; PCIe 4.0/3.0 x16;
Device:         /dev/mst/mt4125_pciconf0

Configurations:                              Next Boot       New
         CQE_COMPRESSION                     AGGRESSIVE(1)   AGGRESSIVE(1)

 Apply new Configuration? (y/n) [n] : n
```

4.5 重启网卡服务
```
[root@AMD-107 ~]# systemctl restart openibd
```

4.6 查看性能统计
```
[root@localhost ~]# mlnx_perf -i enp129s0f0
Initializing mlnx_perf...
Sampling started.
--------
                    tx_packets: 1
                      tx_bytes: 319 Bps              = 0 Mbps
                  tx_csum_none: 1
                       tx_cqes: 1
                     ch_events: 1
                       ch_poll: 1
                        ch_arm: 1
    tx_vport_broadcast_packets: 1
      tx_vport_broadcast_bytes: 319 Bps              = 0 Mbps
                tx_packets_phy: 1
                  tx_bytes_phy: 323 Bps              = 0 Mbps
              tx_broadcast_phy: 1
                tx_prio0_bytes: 323 Bps              = 0 Mbps
              tx_prio0_packets: 1
                   ch46_events: 1
                     ch46_poll: 1
                      ch46_arm: 1
                  tx46_packets: 1
                    tx46_bytes: 319 Bps              = 0 Mbps
                tx46_csum_none: 1
                     tx46_cqes: 1
                          UP 0: 0                    Mbps = 100.00%
                          UP 0: 1                    Tran/sec = 100.00%
```

4.7 查看网卡信息
```
[root@AMD-107 ~]# mlnx_tune
```
