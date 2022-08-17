# <center> bazel学习笔记

[Bazel 学习笔记 (一) 快速开始](https://zhuanlan.zhihu.com/p/411563404)

bazel build -s
--subcommands (-s)
--explain
--verbose_explanations
--local_cpu_resources
--local_ram_resources

bazel clean
bazel clean --expunge

CC=/opt/rh/devtoolset-7/root/bin/gcc bazel build -s //xxx:xxx
