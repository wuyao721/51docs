Python入门指引

# Python是什么样子
- 语法
- 数据类型
- 内置函数

```
#!/usr/bin/env python

"""A tool for parsing and decrypting PPTP packet captures."""

import sys
from chapcrack.commands.CrackK3Command import CrackK3Command
from chapcrack.commands.DecryptCommand import DecryptCommand
from chapcrack.commands.HelpCommand import HelpCommand
from chapcrack.commands.ParseCommand import ParseCommand

__author__    = "Moxie Marlinspike"
__license__   = "GPLv3"
__copyright__ = "Copyright 2012, Moxie Marlinspike"

def main(argv):
    if len(argv) < 1:
        HelpCommand.printGeneralUsage("Missing command")

    if argv[0] == 'parse':
        ParseCommand(argv[1:]).execute()
    elif argv[0] == 'decrypt':
        DecryptCommand(argv[1:]).execute()
    elif argv[0] == 'help':
        HelpCommand(argv[1:]).execute()
    elif argv[0] == 'crack_k3':
        CrackK3Command(argv[1:]).execute()
    else:
        HelpCommand.printGeneralUsage("Unknown command: %s" % argv[0])

if __name__ == '__main__':
    main(sys.argv[1:])
```

# python的优势
    1.语法简单，入门容易
    2.内置list, map, dist等数据结构
    3.自学习系统
    4.丰富强大的库

# python的劣势
    1.执行速度慢
    2.无法利用多核

# Python的使用场合
    1.文本处理
    2.制作工具
    3.强大的网络库
    4.搭建Web网站
    5.爬虫、科学计算、图形处理

# 新手注意要点
    1.严格遵守缩进方式
    2.Python 2.X和3.X的区别，例如print函数

# 如何自学python
    1.了解基本的语法，数据结构
    2.使用交互模式测试
    3.利用python的自助系统

# 推荐读物
    1.《Dive Into Python》
    2.《Python Library Reference》
