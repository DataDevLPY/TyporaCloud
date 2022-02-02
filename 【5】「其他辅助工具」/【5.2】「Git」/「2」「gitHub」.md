# 「2」GitHub

[TOC]



## 一、连接

* origin 为远程连接别名

* master为远程分支名

```shell
# 在本地连接远程库
git remote add origin https://github.com/Peiyang-Felix/huashan.git

# 查看远程库信息
git remote -v

# 推送分支
git push origin master
```



## 二、克隆

```shell
# 在另一文件夹下
git clone https://github.com/Peiyang-Felix/huashan.git
```



## 三、其它操作

* 在repository中setting中可以添加collaborator,之后可以push

```shell
# 抓取
git fetch origin master

# 合并
git checkout origin/master
#合并返回
git checkout master

#最终合并
git merge origin/master

#拉取 pull = fetch + merge
git pull origin master 

```

