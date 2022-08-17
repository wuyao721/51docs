# <center> CPU学习笔记

# x86
```
[root@HW-v5-13 build]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                48
On-line CPU(s) list:   0-47
Thread(s) per core:    2
Core(s) per socket:    12
Socket(s):             2
NUMA node(s):          2
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 85
Model name:            Intel(R) Xeon(R) Gold 6126 CPU @ 2.60GHz
Stepping:              4
CPU MHz:               2601.000
CPU max MHz:           2601.0000
CPU min MHz:           1000.0000
BogoMIPS:              5200.00
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              1024K
L3 cache:              19712K
NUMA node0 CPU(s):     0-11,24-35
NUMA node1 CPU(s):     12-23,36-47
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc aperfmperf eagerfpu pni pclmulqdq dtes64 ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid dca sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch epb cat_l3 cdp_l3 intel_ppin intel_pt ssbd mba ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm cqm mpx rdt_a avx512f avx512dq rdseed adx smap clflushopt clwb avx512cd avx512bw avx512vl xsaveopt xsavec xgetbv1 cqm_llc cqm_occup_llc cqm_mbm_total cqm_mbm_local dtherm ida arat pln pts pku ospke spec_ctrl intel_stibp flush_l1d

```
## SIMD
- sse sse2 sse3 sse4_1 sse4_2 
- avx avx2 avx512f avx512dq avx512cd avx512bw avx512vl

- msr (Model-Specific Registers)
- cqm (Cache Quality Monitoring)
- cqm_llc (Cache Quality Monitoring Last Level Cache)
- cqm_occup_llc (Cache Quality Monitoring Occupy Last Level Cache)
- cqm_mbm_total (Cache Quality Monitoring Memory Bandwidth Monitoring Total Value)
- cqm_mbm_local (Cache Quality Monitoring Memory Bandwidth Monitoring Local Value)

## AMD
- CCD: complex core dia
- CCX: complex core extension

Zen-base AMD平台开发指引
简介
AMD Zen-Base Processor Roadmap
Family	     Zen       Zen 2	Zen 3	Zen 4
年份	     2017      2019 	2020 	?
工艺制程     14nm      7nm 	7nm+ 	5nm?
服务器 (SP3) Naples 32/64	Rome 64/128	Milan'	Genoa

EPYC 7002列命名规则


CPU内部结构
那不勒斯

罗马


Intel


Intel VS AMD
PCIE 4.0
Intel 尚未支持PCIE 4.0，而AMD支持。对于IO高吞吐的应用，这是制约性能的绕过的问题。
内存访问
Intel CPU集成内存控制器（MMU），接连到最多6个通道的内存条；AMD Rome CPU IO Die集成UMC，连接到最多8路内存条，CCD需通过IO Die访问内存。这可以推导出：
     INTEL 内存访问时延更低
     理论上AMD 内存吞吐量比Intel大33.33%
由于Intel理论上可最多支持8路CPU，也即最多支持48通道；而AMD最多支持2路CPU，则最多16个通道。
处理核心
Intel每个核心都有独立的计算单元、L1 Cache、L2 Cache。所有核心共享一个L3 Cache。一般来讲，Intel平台程序处理能力与处理核心数量成正比（实际上可能在L3 Cache、内存、IO遭遇瓶颈）。
AMD由于设计的原因，平台的处理能力瓶颈点更加多，这要求工程师在设计、部署程序时规避这些瓶颈点。
AMD 每个核心都有独立的计算单元、L1 Cache、L2 Cache。4个核心组成一个CCX，每个CCX都有独立的L3 Cache。两个CCX组成一个CCD，在同一个CCD内各个核心访问L3 Cache时延都比较低。AMD采用N个CCD加一个IO Die设计，一方面，所有的处理核心访问内存或者IO都必须通过IO Die，另外一方面，访问不同CCD的L3 Cache也需要通过IO Die。此时IO DIe容易成为系统性能瓶颈点。

BIOS配置
性能 vs 能耗
为了达到更好的处理性能，需要把电源管理相关配置全部都关掉。
	APBDIS=1 (Enable fixed Infinity Fabric P-state control)
	Fixed SOC Pstate=P0
	DF C-states = Disabled (do not allow Infinity Fabric to go to a low-power)
	Determinism Control -> Manual
	Determinism Slider -> Performance
	xGMI Link Max Speed (18Gbps x16)
	prefered IO Bus

延迟 vs 吞吐
NUMA Node per Socket
• NPS0: Interleave memory accesses across all channels in both sockets (not
recommended)
• NPS1: Interleave memory accesses across all eight channels in each
socket, report one NUMA node per socket
• NPS2: Interleave memory accesses across groups of four channels (ABCD
and EFGH) in each socket, report two NUMA nodes per socket
• NPS4: Interleave memory accesses across pairs of two channels (AB, CD,
EF and GH) in each socket, report four NUMA nodes per socket
ACPI SRAT L3 Cache as NUMA Domain
• Disable: Do not report each L3 cache as a NUMA domain to the OS
• Enable: Report each L3 cache as a NUMA domain to the OS

NPS决定了如何访问内存条。NPS0是在两路CPU情况下使用的，所有处理核心可访问所有内存条，由于会影响性能，不建议使用。
NPS1则是在一路CPU内，让所有的核心均衡访问所有内存条。如果存在两路CPU，则它们互相独立，访问内存互不影响。这种配置方式简单粗暴，适合大部分的使用场景。实际上访问内存存在同步的问题，内存利用率将无法到达较高的水平。
NPS2则是将内存一分为二，所有的核心访问自己的那一部分内存。比起NPS1，NPS1访问内存的的时延将更低，内存利用率可达到更高的水平。但是实际上部署更加复杂，要求程序设计更加精巧。
NPS4与NPS2类似，将内存一分为四，所有的核心访问自己的那一部分内存。访问内存的时延将更低，内存利用率可达到更高的水平。部署更加复杂，要求程序设计更加精巧。

L3 Cache As a Numa则决定CCD是否可以访问其他CCD的L3 Cache。打开该开关将禁止CCD访问其他CCD的L3 Cache，这很大程度上的提高系统处理能力。建议打开。


## 性能调优
1、关闭swap
swapoff -a

2、关闭NUMA均衡策略
echo 0 > /proc/sys/kernel/numa_balancing

3、关闭ASLR
echo 0 > /proc/sys/kernel/randomize_va_space

4、配置CPU主频模式
cpupower frequency-set -g performance

5、配置CPU休眠策略，关闭CPU的Deeper模式
cpupower idle-set -d 2
