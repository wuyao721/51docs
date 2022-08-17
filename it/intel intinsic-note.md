官方参考网站： 搜“intel intrinsics guide”

intrin表示intrinsic的意思，故所有相关的头文件都包括mmintrin这个字符串。
gcc编译器集成intel intrinsic的指令集。故所有头文件都放在gcc的源码里面。如：
/usr/lib/gcc/x86_64-redhat-linux/4.8.2/include/

首先查看头文件immintrin.h

``` C
#ifdef __MMX__
#include <mmintrin.h>
#endif

#ifdef __SSE__
#include <xmmintrin.h>
#endif

#ifdef __SSE2__
#include <emmintrin.h>
#endif

#ifdef __SSE3__
#include <pmmintrin.h>
#endif

#ifdef __SSSE3__
#include <tmmintrin.h>
#endif

#if defined (__SSE4_2__) || defined (__SSE4_1__)
#include <smmintrin.h>
#endif

#if defined (__AES__) || defined (__PCLMUL__)
#include <wmmintrin.h>
#endif

#ifdef __AVX__
#include <avxintrin.h>
#endif
#ifdef __AVX2__
#include <avx2intrin.h>
#endif
```

类型命名规则：
__m64    长度64位
__v2si   v表示vector，2表示vector有两个成员，s(single)表示每个成员长度为单个4字节，i表示类型为有符号整形
__v4hi   h(half)表示半个4字节
__v8qi   q(quad)表示四分之一个4字节
__v1di   d(double)表示两个4字节
__v2sf   f表示类型为浮点类型


avx512包含多个文件，分别包含avx512的不同接口。

