## git命令

#### 常用命令

##### 查看配置与版本升级

```
//查看可配置的功能列表
git config
//查看自己已经配置的参数
git config --list
//查看帮助
git config --help
//查看版本
git version
//升级版本
git update-git-for-windows
```

##### 设置账户和密码

```
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

```
//方法1,使用上面的设置命令重新设置一遍
//方法2使用替换命令进行替换
git config --replace-all user.name "name"
git config --replace-all user.email "123@qq.com"
```



#####   检出仓库

```
git clone 仓库地址
```

##### 添加与提交

```
git add <filename>   //添加单个文件
git add *         //添加所有文件
//提交
git commit -m "代码提交信息"
```

#####  推送至仓库

```
git push //默认推送到主分支
git push origin master //可以把 master 换成你想要推送的任何分支
```

#####  分支	

```
//创建分支
git checkout -b feature_x
//切换分支
git checkout master
//删除分支
git branch -d feature_x
//推送分支
git push origin <branch>
```

##### 更新与合并

```
//更新
git pull
//合并其他分支到你的当前分支（例如 master）
git merge <branch>
```

#####  标签

```
git log //查看提交日志
//1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。你也可以用该提交 ID 的少一些的前几位，只要它是唯一的。
git tag 1.0.0 1b2e1d63ff
```

##### 替换本地改动

```
git checkout -- <filename> //获取服务器指定文件
git fetch origin //丢弃所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它
git reset --hard origin/master
//回滚到本地指定版本
git log //查看提交的ID 
git reset --hard commitId  //回滚到指定ID，此时本地修改文件也会被还原到指定commitid版本，注意做好备份
//git只是撤销本地的commit，并不会撤销本地文件
git reset HEAD~
```

​		1.8 其它命令

```
//彩色的 git 输出：
git config color.ui true
//内建的图形化 git：
gitk
```



### 常见问题

##### Q: 使用gitee clone仓库,输入账号密码错误后,如何修改?

> 电脑的控制面板–>用户账户–>管理Windows凭据, 找到gitee,重新输入正确的账号密码

##### 查看人家的项目时,ReadMe文档中的图片无法显示

> win系统下, 修改hosts文件  C:\Windows\System32\drivers\etc\hosts

```
# GitHub Start 
192.30.253.112    Build software better, together 
192.30.253.119    gist.github.com
151.101.184.133    assets-cdn.github.com
151.101.184.133    raw.githubusercontent.com
151.101.184.133    gist.githubusercontent.com
151.101.184.133    cloud.githubusercontent.com
151.101.184.133    camo.githubusercontent.com
151.101.184.133    avatars0.githubusercontent.com
151.101.184.133    avatars1.githubusercontent.com
151.101.184.133    avatars2.githubusercontent.com
151.101.184.133    avatars3.githubusercontent.com
151.101.184.133    avatars4.githubusercontent.com
151.101.184.133    avatars5.githubusercontent.com
151.101.184.133    avatars6.githubusercontent.com
151.101.184.133    avatars7.githubusercontent.com
151.101.184.133    avatars8.githubusercontent.com

 # GitHub End
```

##### 我想clone人家项目,但是很慢,怎么样提速?

> 主流的方法有三个:
>
> 1. 修改host文件   (有人说有效,有人说无效,推荐指数: ★);
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

