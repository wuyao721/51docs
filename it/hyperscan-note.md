# hyperscan学习笔记

# 编译
```
cd hyperscan-5.4.0
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=/data/local/ -DCMAKE_C_FLAGS="-march=core-avx2" -DCMAKE_CXX_FLAGS="-march=core-avx2" -DFAT_RUNTIME=off -DBUILD_STATIC_AND_SHARED=on ..
cmake --build .
```

# 性能优化
- hs_scan基础开销为100ns
- hs_scan的时间开销与扫描文本长度成正比
- hs_scan的时间开销与扫描内容有一定关系，这个关系需要
- 
