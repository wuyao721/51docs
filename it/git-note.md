# <center> git学习笔记

# 概述

## 分支
## HEAD
表示本地数据库当前的版本信息，例如:
git reset -h HEAD 
表示放弃未提交的所有代码，恢复到当前版本

查看.git/HEAD可得知当前版本信息

# 本地数据库
本地数据库包括三个区：
- 工作区(working directory)
- 暂存区(Stage or Index)
- 提交区(Commit History)

# 常用参数

## 禁止分屏
参数**--no-pager**可让git命令输出内容不分屏，全量输出，例如：
```bash
git --no-pager diff
```

本地版本库同步到远程版本库
版本回退
撤销修改

# 常用命令
## 拉取
1. 从远程仓库拉取分支
git clone -b shiro550 git@git.xxx.com:xx-xxx/xxx.git

2. 切换分支
-f参数用于在冲突的时候强行切换
git checkout -f xxx

3. 获取远程最新内容
git fetch

### fetch vs pull

## 提交
1. 从工作区提交到暂存区
git add xxx

2. 从暂存区提交到本地仓库
git commit

消息格式如下：
```
fix: xxx

- xxx1
- xxx2
```
或者
```
feat: xxx

- xxx1
- xxx2
```
中间必须有一个空行

3. 从本地仓库提交到远程仓库
git push

## reset VS revert
reset和revert都用于撤销修改，两个主要不同包括：
- reset删除版本库旧的提交，而revert则产生新的提交
- reset可一次删除多个提交，而revert一次只作用于单个提交
- 某些项目处于安全的考虑会禁用reset

### revert
1. 撤销本地提交
```
git revert --no-commit --edit d4cd52d
```

### reset
1. 撤销本地版本库
```
git reset xxx
```

2. 撤销掉远程的提交
有时候push以后发现代码有问题，不想改了重新提交一次，撤销是一个好选择
```
git reset xxx
git push --force
```

## 查看
1. 查看远程仓库路径
```
git remote -v
```

2. 查看本地仓库分支
```
git branch
```

查看所有分支
```
git branch -a
```

3. 查看历史记录
```
git log --graph
git log --graph
```

4. 比较工作区和暂存区
```
git diff
```

不要分页
```
git --no-pager diff
```

只看文件列表
```
git diff --name-only
```

5. 比较暂存区和本地仓库
```
git diff --cached --name-only
```

# emacs && magit
```
M-x magit-status
```
## 快捷键
- k 撤销本地修改
- e ediff（local、stage/index、remote）
- n 下一个
- p 上一个
- c 提交修改
- P p 推送到remote

## 其他
M-x magit-checkout

# 忽略
忽略文件为##.gitignore##，放在git项目主目录下即可。文件内容例如：
```
*.o
*.d
*.a
*.os
```

## 查看某个文件/目录的修改版本列表
1. 打开这个文件/目录
2. magit-log RET l

## 比较某个文件/目录当前文件和某个版本的区别
1. 在magit-log视图中复制这个版本的代号，如99ba3094
2. 在magit-log视图中按以下步骤：E r 99ba3094 RET RET

# github环境配置
1. 先将证书公钥部分上传到github网站
cat ~/.ssh/id_rsa.pub

2. 登陆**https://github.com/settings/keys**，点击**New SSH key**，并上传证书公钥

3. 使用ssh的方式拉取项目，如：
git clone git@github.com:wuyao721/51docs.git



# 参考资料
[Pro Git](https://www.progit.cn/)
