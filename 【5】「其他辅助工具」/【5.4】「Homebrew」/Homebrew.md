## Homebrew

Homebrew是一款Mac OS平台下的软件包管理工具，拥有安装、卸载、更新、查看、搜索等很多实用的功能。简单的一条指令，就可以实现包管理，而不用你关心各种依赖和文件路径的情况，十分方便快捷。



## 下载

替换镜像源，将下载资源改为国内镜像资源

```
curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install >> brew_install
```

更改文件中的链接资源，将原有的链接资源替换成清华大学的镜像资源

```
BREW_REPO = "git://mirrors.ustc.edu.cn/brew.git".freeze
CORE_TAP_REPO = "git://mirrors.ustc.edu.cn/homebrew-core.git".freeze
```

安装，运行修改了的brew_install，然后是漫长的等待

```
/usr/bin/ruby ~/brew_install
```

![截屏2021-07-15 上午12.42.56](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220004556.png?token=AWS37JMLNL6NGFOWYT6YOTTBTJXDC)

出现这个因为源不通，代码无法下载到本地，解决方法是更换成国内镜像源，执行如下命令，更换到中科院的镜像：

```
git clone git://mirrors.ustc.edu.cn/homebrew-core.git//usr/local/Homebrew/Library/Taps/homebrew/homebrew-core --depth=1
```

然后把Homebrew-core的镜像地址也设置为中科院的国内镜像

```
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```

执行更新brew命令：

```
brew update
```

接着执行brew检测命令：

```
brew doctor
```





![截屏2021-07-15 上午12.45.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-15 上午12.45.01.png)







