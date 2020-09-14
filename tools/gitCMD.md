## git命令

1. #### 常用命令

   1.1  检出仓库

```
git clone 仓库地址
```

​		1.2 添加与提交

```
git add <filename>   //添加单个文件
git add *         //添加所有文件
//提交
git commit -m "代码提交信息"
```

​		1.3.  推送至仓库

```
git push origin master
//可以把 master 换成你想要推送的任何分支
```

​		1.4 分支	

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

​		1.5 更新与合并

```
//更新
git pull
//合并其他分支到你的当前分支（例如 master）
git merge <branch>
```

​		1.6 标签

```
//1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。你也可以用该提交 ID 的少一些的前几位，只要它是唯一的。
git tag 1.0.0 1b2e1d63ff
```

​		1.7 替换本地改动

```
git checkout -- <filename>
//丢弃所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它
git fetch origin
git reset --hard origin/master
```

​		1.8 其它命令

```
//彩色的 git 输出：
git config color.ui true
//内建的图形化 git：
gitk
```



### 常见问题

Q: 使用gitee clone仓库,输入账号密码错误后,如何修改?

A: 电脑的控制面板–>用户账户–>管理Windows凭据, 找到gitee,重新输入正确的账号密码

