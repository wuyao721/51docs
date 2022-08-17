# <center> emacs学习笔记

# 开发技巧

1. 始终在状态栏中显示当前函数
```
(which-function-mode t)
```

2.  查找目录下所有应用字符串的文件
```
M-x grep-find  
grep-a-lot.el
```

3. 当前buffer快速搜索
 - 正向搜索 C-s 方向搜索C-r
 - 搜索光标的内容 C-s C-w
 - 搜索复制的内容 C-s M-y。
 - 搜索中改变大小写敏感 C-s M-c。
 - 搜索的历史记录 C-s M-p/M-n。
 - 搜索单词 M-sw
 - 搜索当前buffer所有出现的 M-so

4. 当前buffer特定字符串绑定颜色
 - M-shp 输入一个正则表达式，给当前buffer中所有匹配该正则的字符串着上指定颜色
 - M-shl 输入一个正则表达式，给当前buffer中包含该正则的行着上指定颜色
 - M-shu 取消着色
 - M-x highlight-symbol-at-point 高亮当前单词

swbuff，让你在最近浏览的buffer之间跳转。

5. 函数间跳转
 - 跳转到(上一个)函数头 C-M-a
 - 跳转到(下一个)函数未 C-M-e

6. 代码折叠
首先是折叠括号和注释的hs-mode。
```
(add-hook 'c-mode-common-hook   'hs-minor-mode)
(add-hook 'emacs-lisp-mode-hook 'hs-minor-mode)
(add-hook 'java-mode-hook       'hs-minor-mode)
(add-hook 'lisp-mode-hook       'hs-minor-mode)
(add-hook 'perl-mode-hook       'hs-minor-mode)
(add-hook 'sh-mode-hook         'hs-minor-mode)
```
然后是折叠#ifdef这样的宏定义的hide-ifdef-mode，用yp-hif-toggle-block（网上抄的）可以切换折叠状态。
```
(hide-ifdef-mode 1)
;;; for hideif
(defun yp-hif-toggle-block ()
   "toggle hide/show-ifdef-block --lgfang"
   (interactive)
   (require 'hideif)
   (let* ((top-bottom (hif-find-ifdef-block))
          (top (car top-bottom)))
     (goto-char top)
     (hif-end-of-line)
     (setq top (point))
     (if (hif-overlay-at top)
         (show-ifdef-block)
       (hide-ifdef-block))))
  
(defun hif-overlay-at (position)
   "An imitation of the one in hide-show --lgfang"
   (let ((overlays (overlays-at position))
         ov found)
     (while (and (not found) (setq ov (car overlays)))
       (setq found (eq (overlay-get ov 'invisible) 'hide-ifdef)
             overlays (cdr overlays)))
     found))
```  
  另外，还有两个网上找的扩展hide-region和hide-lines。
hide-region可以折叠和显示指定的区域，hide-lines可以折叠和显示指定行。默认的hide-region功能有些不够强大。当你折叠一个区域后，她会把这个折叠记录到一个全局的栈里面，调用打开折叠的函数时，就从栈里面找出最近的折叠打开。我改写了一个增强版，首先把记录折叠信息的栈改为buffer-local的，然后增强了打开折叠的功能。
hide-region-hide函数把选择的区域折叠起来。hide-region-unhide-below函数从当前位置往下搜索，找到第一个折叠，把她打开，如果没找到的话就从栈里找最新的折叠打开。hide-region-toggle函数切换当前buffer中所有折叠的状态。如果是折叠的，就全都打开，如果是打开的，就全都折叠。我把我的增强版放在了附件中。

1. ./configure --prefix=/usr/local/emacs --without-xpm --without-jpeg --without-tiff --without-gif --without-png --without-rsvg --without-xml2 --without-imagemagick --with-x-toolkit=no --with-sound=no
2. make -j8
3. make install

# 重要技能

1. emacs命令自动提示
 (ido-mode 1)

2. 管理多个buffer

3. 管理多个shell

4. 让emacs长期运行 
配合screen，emacs能跑一年

5. 作为gdb调试界面

6. 搜索字符串
 - 当前buffer正向搜索 C-s 
 - 当前buffer反向搜索 C-r
 - 当前buffer搜索所有出现 M-s o
 - 目录下搜索指定字符串 M-x grep-find
 - 目录下搜索指定文件名 M-x find-dired

7. 删除/替换字符串
 - 删除长方形文本 C-x r k
 - 插入长方形文件 C-x r t
 - 单buffer查询替换 M-x query-replace
 - 单buffer自动替换 M-x replace-string
 - 多buffer查询替换 M-x ibuffer 先用m，选择buffer，再用Q查询替换
 - 多文件查询替换 M-x dired 先用m选择文件，再用Q查询替换
 - 多文件自动替换 M-x grep-find-replace

8. 重复执行
 - 开始一个宏 M-x ( 
 - 结束一个宏 M-x ( 
 - 重复执行刚刚录制的宏 M-x eeee 
 - 执行4次 M-u 执行16次 M-u M-u 以此类推
 - M-x zzzz简化重复命令

9. 用二进制格式打开文件
 M-x hexl-mode

10. 比较、合并文件/目录
 - 合并文件 M-x ediff
 - 合并目录 M-x ediff-directories

11. 字符串着色
 - M-shp 输入一个正则表达式，给当前buffer中所有匹配该正则的字符串着上指定颜色
 - M-shl 输入一个正则表达式，给当前buffer中包含该正则的行着上指定颜色
 - M-shu 取消着色
 - M-x highlight-symbol-at-point 高亮当前单词

12. 代码折叠
