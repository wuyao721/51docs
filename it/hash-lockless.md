# <center> 哈希表在无锁场景下如何实现

哈希表是一种常见的数据结构，常常用于快速查找数据。  
哈希表多线程场景下的其中一个难题是内存一致性问题，一般称存在内存一致性问题的代码片段为临界区，这样的代码片段主要包括四种操作：读节点内容、插入节点、修改节点内容、删除节点。对临界区加锁是一种常规的做法，但加锁会带来较大的性能开销。  
以下内容主要探讨哈希表在无锁场景下的实现。哈希表主要有两种：开链法和开放地法，以下是两种哈希表的核心结构体。

- 开链法
```
struct ListElem
{
    struct ListElem *next;
    struct ListElem *prev;
    void *key;
    void *value;
};
struct hashTable
{
    struct ListElem *bucket;
    unsigned int bucketSize;
    unsigned int capacity;

    volatile int modifyCnt;
};
```

- 开放地址法
```
struct ListElem
{
    unsigned int status;
    void *key;
    void *value;
    volatile int modifyCnt;
};
struct hashTable
{
    struct ListElem *bucket;
    unsigned int bucketSize;
    unsigned int capacity;
};
```
接下来分别介绍两种哈希表如何实现无锁化。

# 单线程
单线程作为一个基准场景，该场景下读和写都在同一个线程完成。单线程环境下代码无须考虑内存执行循序的问题，无需考虑竞争的问题，也没有临界区的问题。天生就是无锁场景。

# 单写多读（包含单写单读）
方法1：将一个单写多读的哈希表转化成多个单线程的哈希表，每个线程操作自己的哈希表，互不影响。这是一种内存换性能的办法，需考虑实际应用场景。
方法2：内存屏障&乐观锁

内存屏障技术主要是针对写线程。内存屏障有两个层次：编译器层次和CPU层次。编译器层次是指编译器会对代码进行优化，从而改变代码的执行顺序，导致内存一致性问题的出现。CPU层次是指CPU在执行指令的时候为提高执行效率开启乱序执行，导致内存一致性问题。
编译器层次：
gcc编译器内存屏障指令为：
```
#define barrier() __asm__ __volatile__("": : :"memory") 
```
该内联函数指明函数前后顺序为顺序执行。
CPU层次：
CPU层次的内存屏障与具体CPU架构有关，以x86为例：内存屏障相关的主要执行循序包括：读写、写写、写读、读读。其中写写、写读、读读是都是顺序执行的，而哈希表的临界区代码一般情况下是写写。也即哈希表在x86平台无需考虑CPU的内存屏障问题。
以开放地址法的哈希表插入节点函数为例：
```
int insert1(struct hashTable, void *key, void *value)
{
    struct ListElem *elem = get_bucket(key);
    
    if (elem->status == 0) {
       elem->key = key;
       elem->value = value;
       barrier();
       elem->status = 1;
       return 0;
    }

    ....
}
```
	
一个哈希表对节点的操作包括：读节点、插入节点、修改节点内容、删除节点。内存屏障可解决插入节点、删除节点的无锁化问题。对于读节点的无锁化主要通过乐观锁解决，后面会提到。 修改节点内容也需要配合乐观锁协助得以实现。

以开链法的哈希表插入节点函数而如下：
```
int insert1(struct hashTable, void *key, void *value)
{
    struct ListElem *first = get_bucket(key);
    
    struct ListElem *elem = first;
    struct ListElem *tmp;
    do 
    {
        if (eq(elem, key, value)) {
            // 更新节点内容
            tmp = alloc(); 
            tmp->next = ele->next;
            tmp->prev = ele->prev;
            tmp->key = key;
            tmp->value = value;
            barrier();
	    elem->next->prev = tmp;
	    elem->prev->next = tmp;
            myfree(elem);
            return 0;
        }
         tmp = elem;
         elem = elem->next;
    } while(elem != NULL);

    // 找不到，在尾部插入新节点
    elem->next = NULL;
    elem->prev = tmp;
    elem->key = key;
    elem->value = value;
    barrier();
    tmp->next = elem;
    return 0;
}
```
以上的实现技术可解决插入节点、删除节点、修改节点内容的无锁化问题。读节点则通过乐观锁实现。

乐观锁不是锁，甚至不是CPU或者操作系统提供的技术。乐观锁是一种思路，主要针对读线程，读线程步骤如下：
1. 读线程读计数器modifyCnt，记为v1
2. 读线程先读内容key或者value，拷贝到副本key1,value1
3. 读线程读计数器modifyCnt，记为v2
4. 比较v1和v2是否相等，如果相等说明key和value的内容没有被修改，可以直接使用副本key1,value1；
   如果不等，则跳到步骤1
写线程则需要每次对elem内容修改后，对modifyCnt值加1，表示该值被修改了

# 多写多读
多写多读的场景更加复杂，同样的思路，首先考虑能否将一个多写多读的哈希表转化为多个哈希表。
多写多读的哈希表同样可以采用乐观锁的技术，由于存在多写，需要保证modifyCnt的自增是原子操作。
多写多读引入CAS技术，即compare and exchange，在x86平台对应为指令cmpxchg。CAS技术详细介绍可自行上网了解。

对于开放地址法的哈希表，插入节点、删除节点都可以通过CAS解决。同样的，对于读节点无锁化主要通过乐观锁解决。主要困难是“修改节点内容”无法满足内存一致性。
这里可以想到的是在节点内采用自旋锁来解决这个问题（每个节点需要一个自旋锁，这需要额外的内存开销），当然这已经不是无锁化，但是可以将锁竞争的概率降到很低。
值得一提的是：不可采用可能会阻塞线程的互斥锁。（如果更好的办法可欢迎告知。）
```
struct ListElem
{
    unsigned int status;
    void *key;
    void *value;
    volatile int modifyCnt;
    spinlock lock;
};
```

对于开链法的哈希表，由于双向链表的实现技术，较难无锁化。可以想到的是将双向链表转发为单边链表，配合CAS操作实现无锁化，目前仅停留在理论阶段。
当然，自旋锁可解决多写的内存一致性问题，采用链表级别的自旋锁，可大大降低锁竞争的概率。
```
struct ListElem
{
    struct ListElem *next;
    struct ListElem *prev;
    void *key;
    void *value;
    spinlock lock;
};
```
这里只有首节点才需要lock，其他节点lock无效，以上结构体只是伪代码，实际代码实现更加复杂。
