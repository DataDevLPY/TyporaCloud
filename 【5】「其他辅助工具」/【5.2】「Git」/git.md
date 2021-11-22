---------

---------

![image-20210713182951298](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220000834.png?token=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)

------

-------

### 本地库初始化

```
--文件夹初始化
git init

--查询.git文件
ll -lA
```



注意：.git目录中存放的是本地库相关的子目录和文件，不要删除，也不要胡乱修改。

----------

------

### 设置签名

* 形式
  * 用户名：peiyang
  * Email：1005159994@qq.com
* 作用：区分不同开发人员的身份
* 辨析：这里设置的签名和登录远程库（代码托管中心）的账号、密码没有任何关系。
* 命令
  * 项目级别/仓库级别：仅在当前本地库范围内有效
    * git config user.name peiyang_pro
    * git config user.email 1005159994@qq.com
  * 系统用户级别：登录当前操作系统的用户范围
    * git config --global user.name peiyang_glb
    * git config --global user.email 1005159994@qq.com
  * 级别优先级
    * 就近原则：项目级别优先于系统用户级别，二者都有时采取项目级别的签名
    * 如果只有系统用户级别的签名，就以系统用户级别的签名为准
    * 二者都没有不允许



```
--查询签名设置:项目级别
cat .git/config
--查询签名设置：系统级别
cd ~
cat .gitconfig
cat ~/.gitconfig
```

![image-20210713183020915](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220000426.png?token=AWS37JPIZMXBLRLIZOU5BIDBTJWUS)

--------

--------------

### 添加提交以及查看状态

```
--查看工作区、暂存区状态
git status
--创建文件
vim good.txt
--将good.txt加入到分支中
git add good.txt
--将good.txt从分支中拿出来
git rm --cached good.txt
--commit信息，将暂存区的内容提交到本地库
git commit -m "The first time to commit" good.txt
```



-------

---------

### 版本前进后退

```
--查看历史
git log
git log --pretty=oneline
git log --oneline
-- HEAD@{移动到当前版本需要的步数}
git reflog
```

* 基于索引值【推荐】

```
--基于索引去前进后退到版本
git reset --hard [索引号]
```

* 使用^符号：只能后退

```
--基于^的个数后退版本
git reset --hard HEAD^^^
```

* 使用～符号：只能后退

```
--基于个数后退版本
git reset --hard HEAD~3
```



**reset命令的三个参数对比**

* --soft 参数 
  * 仅仅在本地库移动HEAD指针
* --mixed 参数
  * 在本地库移动HEAD指针
  * 重置暂存区
* --hard 参数
  * 在本地库移动HEAD指针
  * 重置暂存区
  * 重置工作区

-----------

--------------

## 永久删除文件后找回

```
--删除文件
rm aaa.txt
--添加到本地库
git commit -m "delete aaa.txt" aaa.txt
--还原找回
git reset --hard [索引值]
```



------

----------

## 比较文件

```
--将工作区的文件和本地库历史记录进行比较
git diff HEAD^ apple.txt
git diff HEAD apple.txt
```



-------

-------

## 分支管理

* 分支的好处
  * 同时并行推进多个功能开发，提高开发效率
  * 各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。

```
--查看分支
git branch -v
--添加分支
git branch hot_fix
--更换分支
git checkout hot_fix
--合并分支:切换到接受修改的分支
git merge hot_fix
```



```
git add .
git commit -m ""
git push
```

