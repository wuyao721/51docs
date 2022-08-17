# <center> Cavium学习笔记

# CN78
1.     Packet Input Unit (PKI)
PKI从SGMII/1000 Base-X, XAUI/DXAUI/RXAUI, ILK, DPI 或者 LBK接口从读取报文，对PKI包括三个部分:FE, PIX, BE。
PKI FE: Packet Input Front-End
PIX: Packet Input Header Extraction subunit
PKI BE: Packet Input Back-End

每个接口(interfaces)都给与分配一个port kind(pkind)，取值范围是0-63。PKI使用pkind来决定一个端口(port，指物理上的)的配置。每一个pkind都有一个对应的初始化style(initial style)，取值范围是0-255，用来决定初始的解析策略(initial parsing policy)。

当报文结束标志到达或者报文的前256个字节到达后，PKI就完成一个报文的解析工作。

当一个报文开始解析，2层、3层、4层或7层的特征也被识别出来，style值也会被更新，用来识别数据流类型。解析过程中，各层起点的指针都被保存下来，可供后面软件的使用。

解析完毕后，最后的style(final style)值将用来计算索引值，也即QPG。这是一个中间步骤，它用来指定SSO组(SSO group)，FPA aura以及channel adder。SSO group决定哪个工作核来解析这个报文。FPA aura标识一个报文的QOS策略(QOS policy)，这包括报文丢弃，Random Early Discard(RED)以及什么时候对入境背压进行报警。Port adder可以用来为同一个端口不同的报文创建不同的端口标识，这可能在priority flow control(PFC) Ethernet中需要这么做。

PKI单元分配并写报文到buffer中，报文格式与应用软件的操作方式是一致。PKI不仅支持可编程的报文大小（通过头和尾的填充），还支持发布一个报文在多个buffer中（以此支持大报文）。

PKI创建和分配WQE，每个WQE包含2层到4层的头指针，硬件解析结果。
PKI

Port Kinds(pkind)

Clusters and Cluster Groups(CL)
Auras
Styles
Channels
Work-Queue Entry(WQE)
重要的字段：
GRP：
TT：Tag Type： UNTAGGED，ORDERED or ATOMIC
TAG：32-bit tag value


2.    Schedule/Synchronize/Order Unit (SSO)


3.    OCTEON III I/O Interfaces
QLM: quad-lane module
SerDes: Serializer/Deserializer
CCPI: Cavium Coherent Processor Interconnect
GSER: General Serializer/Deserializer Unit
PEM: PCI Express Interface

# 包处理流程总结
【PIP/IPD部分】
 1、input部分
PKI模块从packet interface 模块接收到包， 执行头检测和流分类，    把包存在L2/DRAM中，PKI模块创建一个结构体，这个结构体包括SSO进行调度，排序，同步的所需要的信息，PKI模块将包提交给SSO    SSO将这个包放到所选择的对应的QOS输入队列上。
        
2、入口顺序：
入口顺序是PKI上接收的同一个流的数据， 并且被提交给SSO

3、packet data buf
储存接收到的packet data，    IPD从FPA中申请一个buf，    （FPA管理空闲的buf，   FPA的buf空间是在系统启动的时候申请的），然后IPD拷贝packet data的到这个buf，这个buf通常在把包数据读到内部存储器之后由PKO释放内存回到FPA， 

4、
五元组
五元组是一种最常见的根据ip来区分流的网络元素组合

5、流
一个流就是一个共享五元组的包组合

6、
五元组哈希值
五元组哈希通常用于包处理流程，一般用CRC（循环冗余算法），用CRC可以将一个五元组转化成一个16bit的数字，这个16比特值和五元组代表的意义是一样的，五元组用来区分流，PIP负责读包头，然后计算五元组哈希值

7、
tag value是一个32位的数字，第一个tag value 是由PIP/IPD在接收到packet的时候设置的，first tag value由3个区别开的部分组成
31-24位衡为0
23-16位是接收到packet的接口号或者全是1
15-0位是PIP计算的五元组哈希值

15-0位设置为五元组哈希值    的目的是让first tag 来区分流。如果first tag 对于每一个流都有唯一的tag，则OCTION可以平行处理多个流，这些流之间互不干扰，这个tag的全部32位都可以在包处理流程的不同阶段被内核更改，或者来指定出接口

8、tag type，first tag type
一个tag元组包括一个tag value和一个tag type
在包处理流程的不同阶段，tag元组可以被设置为不同值，下面会介绍tag type是怎样影响SSO调度的

9、ORDERED tag type  并行处理
   多个相同流的具有一个OEDERED tag tpe的包可以被多个核心并行处理，因此ORDER并不是表示同一时间只有一个包的意思，这样可以让多个核心同时处理同一个流的多个包，这样可以提升效率
    这种type可以用于允许并行的包处理流程

10、ATOMIC tag type  序列化处理
这种type用来加锁，只有一个具有某tag元组的包会得到这个ATOMIC锁，ATOMIC type特点是同一时间只有一个包在处理，并且严格保证入口顺序。
不同的流可以并行处理，但是同一个流的数据保证one at a time，正在等待的新包（没有获得锁的）是不会被调度的，所以核心不会失速，只有得到锁，包才会被调度。
核心可以改变包的tag type，如果first tag type是ORDERED，核心可以将这个包的ORDERED转换成ATOMIC类型的，这样就能使SSO来保证带有新type的包实现 one at a time，这就为内核提供了一个硬件的原子锁来保护特定区域，新type的包严格按照入口顺序排列，不是按照请求顺序或者随机顺序，当等待锁的时候，包也会被持续的分配给内核，SSO来通知内核何时锁被打开，此时这个包才能被内核处理

当first tag type 就被设置成ATOMIC的，则内核不会将type转换成OEDERED，这样就会使处理速度变慢，所以ATOMIC类型只在需要的时候才能用

11、NULL tag type
这种type既不有序也不序列化，多核心可以处理多个包，SSO不管顺序。    

12、QOS：
   QOS是0-7这8个整数，用来标示包处理的优先权，当收到包，PIP、PID会 为这个包计算QOS，并把这个QOS值写入work队列节点，0、7没有被定义谁优先级高谁优先级低，优先级也没有被要求必须是线性的，通常，优先权是可以配置的，只有一种例外情况，如果爱PKO输出队列中使用固定的优先权，队列的索引越小，则优先级越高，固定优先权输出队列也不是必须被要求的

13、work队列节点
包含tag calue， tag type， QoS value，group， 和一个指向packet data buf 的指针，IPD从FPA中申请WQE buffer，PIP和PID填充这个WQE fileds，并将WQE 发送给SSO，用add_work操作。
 
14、add work操作
用来将一个WQE发送给SSO

15、
Qos 输入队列
SSO有8个QoS 输入队列（0-7），每一个队列对应一个QoS value，当一个WQE被发送到SSO，则SSO会 根据WQE的QoS value将这个WQE放到对应的QoS输入队列上

16、PIP/IPD功能总结
【1】从入接口接收包 
【2】从FPA中申请packet data buffer
【3】将包数据复制到packet data buffer
【4】从FPA中申请WQE
【5】写tag value， tag type， QoS value， group， 和packet data pointer 到WQE中去，
【6】调用add_work流程将WQE发送到SSO，标明QoS value 

【SSO部分】
1、SSO的work描述符：
     SSO有内部额存储器，其中一部分用来创建有限的work描述符号，每一个work描述符都包括SSO调度work给一个core的关键数据，并且能保持包的入口顺序，work描述符的关键字段有WQE指针，tag值，tag类型，QoS值，group值
 2、输入缓存队列和输入溢出队列
SSO在内部存储空间中缓存了每一个QoS队列的队头，每一个WQE有一个work描述符，在SSO的内部存储空间中的QoS队列被成为输入缓存队列，如果输入缓存队列中没有足够的空间，则输入缓存队列的下一个work描述符就存放在位于DRAM的输入溢出队列中，SSO在输入缓存队列有空间的事后会将输入溢出队列的work描述符装填到输入缓存队列中
    输入缓存队列有8个，对应8个QoS
    输入溢出队列也有8个，对应8个QoS组
重填只发生在相同QoS的两个队列之间

3、get_work流程
内核通过get_work流程从SSO获得一个WQE，如果这个流程执行成功，SSO就会返回一个WQE指针给core。SSO会自动选择高优先级的WQE先给core
SDK中提供cvmx_pow_work_request_sync()接口来完成get_work流程，这个接口将物理地址执行get_work流程并被转换成 虚拟地址返回

4、core state 描述符号
在SSO中给每一个core都分配了一个core state 描述符，当一个core成功的执行了一个get_work流程，   一个work描述符就会从输入缓存队列中删除，并被指派给core，则这个指向work描述符的指针就会挂到core state描述符上，work描述符中挂有WQE指针，get_work流程返回一个WQE指针给core

5、调度
调度就是把一个work描述符指派到内核中的过程
调度成功，则位于SSO的core state描述符上就成功的挂上work描述符 ，core实际上得到的是work描述符中挂的WQE指针

6、反调度
deshedule过程会让一个work描述符不被指派给core，SSO会用正常的调度规则重新调度这个work描述符给相同的或者不同的core，一个被deshadule的work描述符相对与从未被调度的work描述符而言有更高的优先级，反调度会打乱系统流程因为这是一个不必需的意外流程，

7、in-flight状态
一旦一个work描述符被调度进一个core，它就被标记为in-flight状态，知道被get-work流程丢弃，或者被设置成NULL类型的tag type，反调度的work描述符也被认为是in-flight状态，但是属于未完成的in-flight状态

8、tag元组
有三种tag类型：ATOMIC，ORDERD，NULL
first tag 元组是由PKI转换为tag元组哈希值的。
tag元组就是tag value和tag type 的组合，这两个元素唯一确定一个tag元组。

9、
in-flight队列
SSO中还有一个in-flight队列，这个队列对于保序，边界锁，包序列化处理有重要作用
当一个work描述符被调度到一个core，这个work描述符就被挂到与其tag元组对应的in-flight队列，每一个tag元组只对应唯一的in-flight队列，所以tag value相同但是tag type不同的两个work描述符属于两个不同的in-flight队列
in-flight  work 描述符连接到一起就构成了一个in-flight 队列，当work被调度进core，它就会挂到对应tag元组的in-flight队列上，当这个work在处理的时候被移动，则它所属与的in-flight队列也会被改变，一个work属于哪个in-flight队列是由它属于的唯一的流（tag元组中tag value）和它所处的处理流程阶段（tag 元组的tag type）二者共同决定的。

10、
ORDERED tag type：并行处理
原文：
The ORDERED tag type means that when a tag switch is requested, the switch will be completed in ingress order. 

ORDERED tag type意思是当一个tag转换被请求，这个请求  会被按照入接口的顺序执行完成
例如：
一个流的数据按照ORDERED类型队列来处理，则因为处理的进度有快有慢，则出SSO的顺序就不回被保证，但是如果在出SSO的时候加上tag type转换的话，则出SSO的顺序就会被保证

11、ATOMIC tag type :锁定关键区域
同一个in-flight队列中的具有某个元组的work 在同一时间只有一个具有ATOMIC tagtype，这就是包连接锁的执行原理，SSO能保证锁是以入接口顺序获得，而不是按照请求ATOMIC锁的顺序。
    如果first tag type 被设置为ATOMIC，并且core没有将tag type 转换成ORDERED，则同一个流的包处理速度就慢于ORDERED类型的，因为同一个流同时只有一个包在处理，如果有16个core，且有16个ATOMIC流，则系统可以同时处理 16个包

12、选择下一个WD（work描述符）调度，跳过未被调度的work描述符
SSO调度器可以调度能跑在一个核心上的下一个最高优先级的WD。
QOS优先权和其他可配置的调度参数共同决定了一个WD的优先级
不考虑优先权，在输入队列中具有ATOMIC类型的WD，在其前面一个具有ATOMIC优先权的WD没有被释放ATOMIC锁的时候不会被调度，后面的WD如果被指派给一个core，则需要等待。这个core将会启动空闲模式来等待前面的ATOMIC锁，
反调度的WD会被跳过，如果新的WD被指派给了core，
ORDERED类型可以被并行的调度进core，永远不会发生反调度现象
ATOMIC类型在其对应的tag元组被锁定时不能被调度，只能等待这个lock

13、SSO总结：
【1】SSO从IPD接收WQE，并将WQE放进对应的QoS队列，QoS队列优先级在系统初始化的时候就被设置好了
【2】SSO有内部存储空间用来存储work描述符和core state描述符，每一个输入缓存队列的队头都放在SSO的内部存储器中，输入缓存队列是一个work描述符连接成的队列，如果没有足够的work描述符来维持这个输入缓存队列，这个队列就会继续从DRAM的溢出缓存队列中重装work描述符。
【3】当一个core成功的执行了get_work流程，SSO调度进程就会自动的从优先级最高的输入缓存队列中找到最高优先级的work描述符调度给core，这个work描述符的指针就会挂到core state描述符中，这个work描述符也会挂在与其tag元组匹配的in-flight队列上，core得到这个work描述符中的WQE指针。
【4】具有ORDERED类型tag的work描述符可以按照入接口顺序并行的被多个核心处理
        具有ATOMIC类型的tag的work描述符在同一时间内只能有一个包在按入接口顺序被处理
14、临界区锁：one-at-a-time Access
临界区，类似编码中的共享区，被packet-link锁所保护，    只在ATOMIC tag type 下才起作用，当一个内核需要访问临界区，它就会将WD的tag type由ORDERED转换成ATOMIC

15、
switch_tag 流程
一个core可以执行一个switch_tag流程来转换一个WD 的tag type 和tag value，或者二者同时转换，core可以启动tag-switch流程来把ORDERED切换成ATOMIC来得到一个锁，以便锁住临界区，可以再次启动tag switch流程改变tag元组来实现解锁。

16、switch tag 序列
同一个流的包，需要在在同一个序列内切换tag元组，例如同一个序列内所有包都要从ORDER-5切换到ORDER-6，如果一些包不在同一个序列内切换tag，则这些包就会失序，如果在包处理流程的两个步骤中有相同的tag元组则包会混淆，包处理流程就不会按照严格的步骤来走

17、 core里的switch操作完成标志位
当core请求switch操作的时候，它请求SSO改变WD的tag元组，这种tag switch不需要立即转换完成，对于OEDERED和ATOMIC tag类型来说，
在这个包的tag被switch之前，这个包必须成为in-flight队列的队首，并且如果想要转换成ATOMIC类型，还需要等待ATOMIC锁，
SSO会通知core何时switch流程完成
CAVIUM的网络硬件寄存器30存放的switch tag请求状态，RDHWR过程可以用来读取ZH这个寄存器，这个寄存器不是普通的30号寄存器
当core请求tag switch 流程，此core的switch 完成标志位置为0 ， 当switch完成，SSO会把这个标志位置为1
如果core想switch成ATOMIC类型，它必须等到switch已经完成的通知，当switch完成，core就会保证得到ATOMIC锁，并且可以安全的访问临界区
一个core只能有一个未完成的switch，所以每一个core只需要一个switch标志位即可。
switch标志位的三个值：
【1】X表示这个值不重要，switch还没有开始
【2】0表示switch正在进行中，还没转换完
【3】1表示转换完成

18、初始化in-flight队列
tag switch请求开始的WD所在的in-flaght 队列 叫做初始in-flight队列

19、目标in-flight队列
tag switch 过程完成的WD所在的in-flight队列 叫做目标in-flight队列

20、tag switch processing
tag switch 过程有多个步骤，每一个步骤都被SSO控制，每一个步骤要在switch过程完成前完成。

21、tag switch     过程步骤
在core请求switch 和switch 完成之间，有很多SSO控制的步骤：
【1】内核请求一个tag switch，core的 switch 完成标志位置为0
【2】WD等待成为initial in-flight队列的队头
【3】WD被移动到target in-flight队列的队尾
【4】WD等待转换
        情况1：如果想要转换成ATOMIC状态，则WD需要等待成为taget in-flight队列的队头    
        情况2：如果想转换成ORDERED状态，则WD立即完成，不许要任何延迟，因为ORDERED的执行流程是并行的
【5】当任何等待过程都完成，则switch过程完成，SSO将完成标志位置为1
在switch的时候WD一直保持同core的连接
一个WD只有在initial in-flight队列中达到队头位置，它才能被塞进target队列中去，这是SSO实现众多保序功能中的一种。

22、tag switch：由ATOMIC到ORDERED
这个过程是用来释放一个ATOMIC锁，在这种情况下，即使只有在initial in-flight队列头部的WD在同一时间持有ATOMIC锁，当core请求转换成ORDERD类型的时候，WD会立即塞进target in-flight 队列，因为target in-flight队列是ORDERED的，这些WD可以并行的工作，WD不需要等待自己成为target in-flight队列的队头就可以直接完成转换，SSO就会将转换完成标志位置为1

23、从ORDERED到ORDERED
这种转换最少使用

24、总结
为了保护临界区，core执行switch-tag流程来吧ORDERED转换成ATOMIC，把WD从initial in-flight队列移动到target in-flight队列
【1】当    initial in-flight队列是ORDERED的，   则WD必须成为这个队列的头，才能被move
【2】当 initial in-flight队列是ATOMIC的，WD就已经是这个队列的队头了
【3】WD是从 initial in-flight队列头移动到target in-flight队列尾的
【4】如果target in-flight队列是ORDERED的，则switch会被立即执行完成
【5】如果target in-flight队列是ATOMIC的，则WD必须等到成为这个队列队头才能执行转换

25、PKO
当core准备好发送这个包了，则core会发送transmition命令给一个最优先的PKO输出队列，内核根据SSO的同步和保序特点来选择PKO队列，并给它加锁，来保证包传输命令是按照入接口的顺序传送到PKO队列中的（将包按入接口顺序发送到PKO队列是一种选项，不是必须的    ）
PKO会从PKO队列队尾删除命令，从L2/DRAM中读取包数据，直接将包数据传送到选择好的出接口，PKO可以通知core包已经发送完成 

26、PKO 输出端口
PKO支持多达40个输出端口，不同端口对应不同物理接口
27、PKO输出队列
PKO有多达256个输出队列，这些输出队列分布在输出端口上，输出队列可以有不同的优先级，这个优先级是在系统启动的时候初始化的，这个优先级是用来保证同一个流的包会被按照入接口顺序保序输出同一个输出队列上。

28、
PKO输出队列在端口的分布
输出队列数目多余端口，所以多个输出队列会对应上一个端口，这个对应关系也是系统启动的时候初始化的

29、
选择PKO输出队列
每一个PKO输出队列都由一个tuple五元组标识，标识流的tag值会被重写为标识输出队列的tag值

30、

释放WQE buffer
在创建了包发送命令之后，core就会释放WQE所占的buffer回FPA，因为WQE在发送出去之后就再也不需要了，这个操作是在包发送命令传输到输出队列之前就做完了的。

31、锁定PKO输出队列
core会对响应tag元组的输出队列执行一个switch_tag流程吧tag type设置成ATOMIC类型，这会锁住PKO输出队列，每一个PKO输出队列都会被一个唯一的tag元组所标识，因为每一个队列都有自己的锁

32、按照入接口的顺序输出包
包顺序会被包处理流程保存，如果满足以下条件：
【1】在包处理的时候只使用ORDERED和ATOMIC类型的tag
【2】所有在同一个流的包都经过相同的tag switch序列
【3】ATOMIC锁被用来保证同一个流的包会被one-at-a-time方式发送到PKO输出队列
【4】同一个流的包发送到同一个PKO输出队列 
当最后的switch tag 流程把包都转换成ATOMIC执行完毕后，WD就会被保证入接口顺序，因此包发送命令就会按照入接口顺序传送到PKO
注意：同一个流的包必须吧它们的包发送命令发送到发送到同一个输出队列来保证包发送的入接口保序性，如果入接口顺序不重要，则包发送命令可以发送到不同的输出队列上，在这种情况下，目的设备就要重新组装数据包为正确顺序

33、
写入输出队列，然后释放锁
一旦内核持有锁，它就会向输出队列发送命令

34、释放WD和锁
传输命令在发送给输出队列之后，内核就会释放WD和packet-linked锁
get work流程会
【1】释放WD
【2】释放packet-link锁
【3】返回一个新的WQE给core

35、PKO直接将包数据发送到输出物理接口
PKO调度器会直接从输出队列尾部删除包发送命令，且将包数据发送到选择的输出物理端口

36、释放包数据buffer回FPA

37、
当处理玩包数据后，core会switch tag元组，  tag type 会被设置为ATOMIC，tag value与输出队列元组相关，这个流程会对输出队列加锁，然后释放这个锁，然后释放WQE和WD，当PKO吧包数据读到自己的内部存储器之后就会释放packet data buffer，。
如果包必须按照出接口发送，则同一流的包必须加入同一个输出队列，

38、单流例子：
单流tag元组可以用作：
• Separate different flows.
• Keep packets in ingress order.
• Allow packets to be processed in parallel whenever possible.
• Protect critical regions with ATOMIC locks, while maintaining ingress order.
• Select the PKO Output Queue, lock the queue, and send packets to the PKO in ingress order.

39、多流
In this simplified example, only one flow was examined. In the ideal system, each flow has a
unique tag value. Thus, multiple flows are processed in parallel, which is a very high-performance
model. Flows may use independent packet-linked locks (for instance, there is one TCP/IP control
block per flow, each protected by a different lock), or flows may share locks with other flows (for
instance, use a common lock to access a shared PKO Output Queue).

40、总结
【1】SSO利用tag元组来保证多流的入接口包顺序
【2】每一个WQE都有一个关联的WD，这个WD有自己的tag元组，并根据自己的元组插入到对应的in-flight队列，当switch-tag流程执行时，WD会按照入接口顺序从initial in-flight队列移动到target in-flight队列
【3】用ORDERED类型，爆出里流程就可以高速并行处理，用ATOMIC类型，临界区就会被packet-linked锁保护
【4】同一个流的所有包要走同一个tag switch序列，这是很重要的，否则入接口顺序就不会被保持 
【5】tag 值和ATOMIC tag type用来选择和锁定一个PKO输出队列，目的是保证同一个流的包是按找入接口顺序发送出去的

41、包处理流程被区分为5个相：
【1】包输入
    处于流中的包被按顺序接收，PIP/IPD完成包头部检查和区分流，IPD设置first tag元组，IPD从FPA中申请需要的buffer，PIP/IPD直接将包数据传送到L2/DRAM上的包数据buffer，IPD执行一个add_work流程吧WQE指针送到SSO的输入队列上去，这个过程是基于QoS优先级的
【2】SSO调度新work到内核
core执行一个get_work流程来得到一个WQE指针，这个WQE指针包含有包数据，因为first tag type是ORDERED类型，所以这些包    可以并行的被多个核处理。
【3】加锁临界区 one-at-a-time access
当一个core需要lock一个临界区，它会执行一个switch-tag流程，把type转换成ATOMIC，当switch流程完成，SSO会设置switch完成标志为1，core就会得到ATOMIC锁，然后可以访问临界区。
【4】解锁临界区然后重新并行执行
当一个core需要解锁一个临界区，它会执行一个    switch tag流程，吧type转换为ORDERED，一定要指定一个新的tag值，这个tag值不能和之前用的tag值相同
【5】包输出
当一个包准备输出，core执行switch-tag流程吧tag type 转换为ATOMIC，这个tag value是用来选择PKO输出队列的，一旦core得到了ATOMIC锁，这个core就会发送包传输命令给PKO输出队列，PKO负责管理包优先级，并send这个包到合适的端口，如果同一个流都被发送到 同一个PKO输出队列，则就能保证入接口顺序。



# 开发环境搭建
1. 保存开发包到${ROOT}/OCTEON-SDK.tar.gz
2. 进入目录${ROOT}，解压。
3. 初始化开发环境
pushd ${ROOT}/OCTEON-SDK 
source env-setup <OCTEON_MODEL>
popd

其中<OCTEON_MODEL> 见文件octeon-models.txt，比如OCTEON_CN68XX

可以将其写成脚本，登录时自动调用

4. cp example/debugger; make; cp test.out /var/lib/tftpboot/


1. 编译rootfs
cd ${ROOT}/OCTEON-SDK/linux/embedded_rootfs
make menuconfig 保存配置
rm rootfs.cpio* // 更新文件系统内容后调用
make all initramfs -j16


2. 编译NTC
cd 

2. 编译kernel
cd ${ROOT}/OCTEON-SDK/linux
make kernel -j16
cp ./kernel_2.6/linux/vmlinux /var/lib/tftpboot/

重启分流器


修改application.mk，增加LDFLAGS_LOCAL

$(TARGET): $(CVMX_CONFIG) $(OBJ_DIR) $(OBJS) $(LIBS_LIST)
        $(CC) $(OBJS) $(LDFLAGS_PATH) $(LIBS_LIST) $(LDFLAGS_GLOBAL) $(LDFLAGS_LOCAL) -o $@


gdb调试：
得用专用的调试工具：
${ROOT}/OCTEON-SDK/tools-gcc-4.3/bin/mips64-octeon-linux-gnu-gdb

u-boot设置
setenv initipc 'namedalloc ntc_ipc 0x4000000'
setenv bootcmd 'run initipc;run tftpse;run tftpos'

linux程序启动引导程序为
cvmx-app-init-linux.c setup_cvmx_shared main
Cavium提供的代码会有标准输出，为了避免这么的事情，需要修改其代码，将所有的标准输出删掉。


