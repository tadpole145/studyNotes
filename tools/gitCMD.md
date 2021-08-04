## Git, Github使用以及在IDEA中的集成



#### git命令

![git命令](https://gitee.com/tadpole145/images/raw/main/20210804182459.png)

##### 查看与升级

```java
//查看可配置的功能列表
git config
//查看自己已经配置的参数
git config --list
//查看帮助
git config --help
//查看版本
git version
//升级版本,由于网络原因,特别慢,建议通过镜像下载客户端,手动覆盖安装新版
git update-git-for-windows
//查看状态
git status
//查看所有提交的完整日志 
git log 
//查看所有版本id
git reflog
```

##### 设置账户和密码

```java
//保存账号密码,执行后,输入一次即可
git config --global credential.helper store
//清除保存的账号密码
git config --global credential.helper reset
//全局账户(电脑上所有的git仓库起作用)
git config --global user.name  "yourname"  
git config --global user.email  "your email"
//当前账户(只对当前仓库起作用)
git config  user.name  "username"  
git config  user.email  "email"
//局部变量覆盖全局变量,局部优先,没有设置才会去使用全局的.
```

##### 修改账户密码

```java
//方法1,使用上面的设置命令重新设置一遍
//方法2使用替换命令进行替换
git config --replace-all user.name "name"
git config --replace-all user.email "123@qq.com"
```



#####   检出仓库

```
git clone 仓库地址
```



##### 创建本地仓库

在本地文件目录下,输入命令

```java
git init
```



##### 添加与提交

```java
git add <filename>   //添加单个文件
git add *         //添加所有文件
//提交
git commit -m "代码提交信息"
```

##### 删除

```java
//删除暂存区指定文件名的文件
git rm --cached xxx
```



#####  分支

```java
//创建分支
git branch 分支名称
//查看分支版本
git branch -v
//切换分支
git checkout 分支名
//合并其他分支到你的当前分支 
git merge 分支名
//创建并切换到指定分支(2步合并)    
git checkout -b 分支名
//删除分支
git branch -d 分支名
//推送到指定分支
git push 别名 分支名
//切换到指定版本ID，此时本地修改文件也会被还原到指定commitid版本，如果还未提交,请提交或者注意做好备份再切换
git reset --hard commitId 
```



#####  标签

```java
//查看完整提交日志
git log 
//查看7位版本号
git reflog
//1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。你也可以用该提交 ID 的少一些的前几位，只要它是唯一的。
git tag 1.0.0 1b2e1d63ff
```

##### 替换本地改动

```java
//获取服务器指定文件
git checkout -- <filename> 
//丢弃所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它
git fetch origin 
//回滚到本地指定版本
git reset --hard origin/master

 
//git只是撤销本地的commit，并不会撤销本地文件
git reset HEAD~
```

##### 	 其它命令

```
//彩色的 git 输出：
git config color.ui true
//内建的图形化 git：
gitk
```



#### github使用

#####  推送远程仓库

```java
//默认推送到默认分支,适用于非第一次push
git push 
//推送到指定分支
git push origin 分支名 
    
//查看当前所有远程地址别名 
git remote -v 
//起别名 
git remote add 别名 远程地址
//推送本地分支上的内容到远程仓库    
git push  别名  分支
git push 仓库地址 分支
```

###### 情景一:   本地有文件,无远程仓库,推送到远程仓库

1. 本地文件目录先git init, 然后add, commit
2. 创建github远程仓库
3. 给远程仓库地址起别名,然后推送到指定分支, 或者直接使用地址

tips: 如果不带分支会提示错误,按照错误提示设置后,下次推送可以免掉仓库地址/别名  分支了.![image-20210802172939711](https://gitee.com/tadpole145/images/raw/main/20210802172947.png)



###### 情景二:  本地无文件,远程仓库存在,拉取后修改并推送

1. 本地目录终端输入 git clone 远程仓库地址
2. 修改后,正常add, commit,然后git push. 

tips: clone远端仓库,会给默认起个别名叫origin.可以通过git remote -v命令查看.

##### 拉取远程仓库最新代码

```java
git pull
git pull 别名 分支名
```



##### github使用SSH免密登录

<img src="https://gitee.com/tadpole145/images/raw/main/20210803093556.png" alt="image-20210803093556221" style="zoom: 50%;" />

如果C:\Users\admin\ .ssh文件夹不存在,则会提示你需要添加一个公钥才可以使用ssh免密登录.如果有,则可以省略掉生成公钥这一步,直接配置就好了.    步骤如下:

1. 进入C:\Users\admin目录下,打开git bash终端, 输入命令

	```
	ssh-keygen -t rsa -C github用户名
	```

	然后连续敲3次回车键,可以看到C:\Users\admin目录下已经生成了.ssh文件夹,包含如下几个文件,然后使用文本打开id_rsa.pub,复制里面的公钥.![image-20210803094239163](https://gitee.com/tadpole145/images/raw/main/20210803094239.png)

2. 打开github账户,点击设置,然后点击SSH侧边栏,添加SSH keys,将之前复制的公钥复制进去.名字随便取![image-20210803102938052](https://gitee.com/tadpole145/images/raw/main/20210803102938.png)



3. 使用ssh进行推送与拉取... 

	```java
	git push git@github.com:tadpole145/test.git main
	git pull git@github.com:tadpole145/test.git main
	```



##### github向第三方开源库贡献代码

1. 登录自己github账户,fork人家的代码
2. checkout到本地进行修改
3. add, commit ,push到自己github仓库
4. 点击仓库的pull requests, 创建一个新的请求,根据提示填写具体内容,然后提交.![image-20210803112534636](https://gitee.com/tadpole145/images/raw/main/20210803112534.png)
5. 原开源作者会收到你推送的内容,会决定是否合并你提交的代码.一旦接受,你就称为了贡献者.





#### IDEA集成Git

##### 配置git忽略文件

###### 有哪些文件应该忽略

- 项目运行生成的中间文件
- 系统环境差异生成的配置文件
- 一些与项目实际功能无关,不参与服务器部署运行的文件.

###### 如何忽略

1. 创建忽略规则文件xxx.ignore,一般建议是git.ignore.存放路径随意,一般建议在用户目录C:\Users\用户名下,与gitconfig 文件在一起.

2. 忽略内容, 除以下外,可以根据需求自定义添加

	```properties
	# Compiled class file 
	*.class 
	# Log file 
	*.log 
	 
	# BlueJ files 
	*.ctxt 
	 
	# Mobile Tools for Java (J2ME) 
	.mtj.tmp/ 
	 
	# Package Files # 
	*.jar 
	*.war 
	*.nar 
	*.ear 
	*.zip 
	*.tar.gz 
	*.rar 
	 
	#virtual machine crash logs, see http://www.java.com/en/download/help/error_hotspot.xml 
	hs_err_pid* 
	 
	.classpath 
	.project 
	.settings 
	target 
	.idea 
	*.iml 
	```

	3. 在C:\Users\用户名目录下找到.gitconfig , 然后引用忽略配置文件.![image-20210803115507534](https://gitee.com/tadpole145/images/raw/main/20210803115507.png)

##### IDEA集成Git

在IDEA的Setting--version Control-->Git中, 关联自己本地的Git客户端.![image-20210803141109187](https://gitee.com/tadpole145/images/raw/main/20210803141109.png)



##### 使用IDEA管理项目版本

1. 使用idea打开项目后,点击菜单栏VCS-->import into Version Control--->Git, 然后选择要管理的项目.
2. ![image-20210803141634287](https://gitee.com/tadpole145/images/raw/main/20210803141634.png)



2. 选择项目名,右键-->Git-->add, 将所有文件添加到暂存区

![image-20210803142221322](https://gitee.com/tadpole145/images/raw/main/20210803142221.png)

3. 添加完成后,重复上面的步骤, 在Git选项中, 选择Commit Directory.

###### 版本切换

 点击底部的Git菜单,然后选择想要的版本,右键弹出菜单,选择checkout即可切换到指定版本

![image-20210803145928023](https://gitee.com/tadpole145/images/raw/main/20210803145928.png)

###### 创建分支

![image-20210803154450935](https://gitee.com/tadpole145/images/raw/main/20210803154451.png)

当然,还可以从当前版本创建分支,比如直接点击4,也可以创建.但是从示意图这个可以选择历史版本创建分支,我认为是最方便自由的.

###### 切换分支

![image-20210803155259241](https://gitee.com/tadpole145/images/raw/main/20210803155259.png)



###### 合并分支

**方法一, 选择项目,右键, 步骤如下图所示.**

![image-20210803161152562](https://gitee.com/tadpole145/images/raw/main/20210803161152.png)

![image-20210803161515447](https://gitee.com/tadpole145/images/raw/main/20210803161515.png)



**方法二, 从底部分支入口合并**

![image-20210803161657456](https://gitee.com/tadpole145/images/raw/main/20210803161657.png)



Tips:  方法二,点击合并会直接合并,不会向方法一提供日志功能, 使用哪种根据自己喜好来决定.



#### IDEA集成Github

##### 登录github账号

1. IDEA设置中的pluign中查看是否有github插件, 如果没有则安装

![image-20210803165233852](https://gitee.com/tadpole145/images/raw/main/20210803165233.png)

2. 在设置中的github 添加账号

![image-20210803165613499](https://gitee.com/tadpole145/images/raw/main/20210803165613.png)

由于账号密码登录经常显示404, 下面介绍通过token的登录方式

###### 获取登录Token

1. github账号--->setting--->developer settings

![image-20210803170909429](https://gitee.com/tadpole145/images/raw/main/20210803170909.png)

2. 选择Personal access tokens--->create new token,当然如果有,就没必要再次生成了.记住就好了.

![image-20210803171435492](https://gitee.com/tadpole145/images/raw/main/20210803171435.png)

3. 添加token,开放权限与有效期均自定义选择
4. 将生成的token复制到idea中,成功后会显示自己的账号.

##### 分享项目到github

![image-20210803180338200](https://gitee.com/tadpole145/images/raw/main/20210803180338.png)



##### 推送与拉取

![image-20210803180949319](https://gitee.com/tadpole145/images/raw/main/20210803180949.png)

##### IDEA 克隆仓库代码

![image-20210803182139413](https://gitee.com/tadpole145/images/raw/main/20210803182139.png)

![image-20210803182255727](https://gitee.com/tadpole145/images/raw/main/20210803182255.png)





#### Gitee使用

##### 结合git客户端

在gitee上创建一个新仓库,就会出现如下图所示的入门教程.

![image-20210804112726723](https://gitee.com/tadpole145/images/raw/main/20210804112726.png)

##### IDEA集成Gitee

1. idea中安装gitee插件
2. 操作参见github, 同样是add文件,commit,share...
3. pull,push,clone 与github一致...



##### gitee 迁移github项目

![image-20210804114505544](https://gitee.com/tadpole145/images/raw/main/20210804114505.png)



#### git常见问题



##### Q: 使用gitee clone仓库,输入账号密码错误后,如何修改?

> 电脑的控制面板–>用户账户–>管理Windows凭据, 找到gitee,重新输入正确的账号密码

##### 查看人家的项目时,ReadMe文档中的图片无法显示

> 由于ip被墙导致国内经常无法连接github的ip地址, 可以在win系统下, 修改hosts文件  C:\Windows\System32\drivers\etc\hosts

```properties
# GitHub520 Host Start  每小时更新,请自行参考官方开源库设置
140.82.113.25                 alive.github.com
140.82.112.25                 live.github.com
185.199.108.154               github.githubassets.com
140.82.114.21                 central.github.com
185.199.108.133               desktop.githubusercontent.com
185.199.108.153               assets-cdn.github.com
185.199.108.133               camo.githubusercontent.com
185.199.108.133               github.map.fastly.net
199.232.5.194                 github.global.ssl.fastly.net
140.82.112.3                  gist.github.com
185.199.108.153               github.io
140.82.114.4                  github.com
192.0.66.2                    github.blog
140.82.112.6                  api.github.com
185.199.108.133               raw.githubusercontent.com
185.199.108.133               user-images.githubusercontent.com
185.199.108.133               favicons.githubusercontent.com
185.199.108.133               avatars5.githubusercontent.com
185.199.108.133               avatars4.githubusercontent.com
185.199.108.133               avatars3.githubusercontent.com
185.199.108.133               avatars2.githubusercontent.com
185.199.108.133               avatars1.githubusercontent.com
185.199.108.133               avatars0.githubusercontent.com
185.199.108.133               avatars.githubusercontent.com
140.82.113.10                 codeload.github.com
52.217.135.113                github-cloud.s3.amazonaws.com
52.217.74.20                  github-com.s3.amazonaws.com
52.217.0.11                   github-production-release-asset-2e65be.s3.amazonaws.com
52.216.250.204                github-production-user-asset-6210df.s3.amazonaws.com
52.217.74.124                 github-production-repository-file-5c1aeb.s3.amazonaws.com
185.199.108.153               githubstatus.com
64.71.144.202                 github.community
185.199.108.133               media.githubusercontent.com


# Update time: 2021-08-04T10:15:00+08:00
# Update url: https://raw.hellogithub.com/hosts
# Star me: https://github.com/521xueweihan/GitHub520
# GitHub520 Host End


```

##### 我想clone人家项目,但是很慢,怎么样提速?

> 主流的方法有三个:
>
> 1. 修改host文件  (参考上面的开源库) ;
> 2. 使用gitee克隆,然后去gitee下载  (推荐指数: ★★★);
> 3. 使用gitclone命令 (推荐指数: ★★★★★), 使用教程参考[官网](https://www.gitclone.com/),推荐方法一,方法二在push项目时候出错.
> 4. 使用github镜像下载, [镜像一](https://github.com.cnpmjs.org), [镜像二](https://hub.fastgit.org) (推荐指数: ★★★★★);

##### 我push我的项目,每次push都需要输入账号密码,怎么解决?

> 参考上面git命令的设置账号密码,设置如下指令即可达到主需要输入一次性账号密码

```
//保存账号密码,执行后,输入一次即可
git config --global credential.helper store
//清除保存的账号密码
git config --global credential.helper reset
```



