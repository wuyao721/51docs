# GCC学习笔记

gcc常见参数

-O 优化
-O0 没有优化
-O1 一级优化
-O2 二级优化
-03 三级优化
-D
-I
-L
-l
-ggdb 加入gdb的调试信息（还有-g3 -g2 -g1等等）
-Wall 输出所有警告
-fPIC 地址无关代码
-shared 
-static 链接时全部使用静态库链接（如果不指定，就使用动态库）
-Wl,-Bstatic 对接下来的链接使用静态库
-Wl,-Bdynamic 对接下来的链接使用静态库
一个混用的例子：
-Wl,-Bstatic -ltestlib -Wl,-Bdynamic -ltestdll
