

### 个人博客搭建

直接使用md写文档,然后通过hexo发布在github page上.

使用到的工具(安装Node后请先**添加国内镜像源**):

- [Node](https://nodejs.org/zh-cn/)
- [git](https://git-scm.com/),  [镜像下载](https://npm.taobao.org/mirrors/git-for-windows/)
- [github](https://github.com/)
- [hexo](https://hexo.io/zh-cn/)

参考文章:  [hexo官方教程★★★推荐](http://blinkfox.com/2018/09/28/qian-duan/hexo-bo-ke-zhu-ti-zhi-hexo-theme-matery-de-jie-shao/#toc-heading-1); [官方github地址](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md);  [Hexo+Github博客搭建完全教程](https://sunhwee.com/posts/6e8839eb.html#toc-heading-2)

##### 写在前面

千万不要偷懒,搭建博客系统要先学学node知识,git知识,hexo官方文档,再开始搭建自己的博客.自己踩坑git的main,master主干/分支, githubPage显示,hexo与node版本不兼容等....其实你都按照最新的文档就不会踩坑了.

由于参考文章教程写的非常详细,直接照着来就行,这儿记录下hexo的几个常用操作按钮就好.

- hexo init <xxx>
- hexo version  显示版本

- hexo clean 清除生成的文件
- hexo f    强制重新生成文件
- hexo g 生成网页, 只生成有变动的网页
- hexo s  启动服务器,可以本地预览,默认情况下，访问网址为： `http://localhost:4000/`
- hexo publish [layout] <filename>  发表草稿
- hexo d 部署

网上一些博客写的X分钟搭建个人blog等,我不太建议直接copy人家的源码修改,还是自己看看[hexo配置文档](https://hexo.io/zh-cn/docs/configuration),只有了解了,才知道怎么写. 

**Tips**: 配置文档_config.yml里面的写法都是==冒号后面添加**空格**==,再跟value值,不然会报错.

##### 创建新文章

- 方法一,根据模板自动生成

  > 首先在博客根目录下右键打开`git bash`，安装一个扩展`npm i hexo-deployer-git`。
  >
  > 然后输入`hexo new post "article title"`，或者`hexo new "xxx"`新建一篇文章。
  >
  > 然后打开`...\MyBlog\source\_posts`的目录，可以发现下面多了一个文件夹和一个`.md`文件，一个用来存放你的图片等数据，另一个就是你的文章文件啦。默认是以根目录/scaffold/post.md文档为模板生成的,模板里面有一些配置好的参数,修改就行.

- 方法二, 手动生成

  > 自己在`...\MyBlog\source\_posts`目录下新建md文档,然后typora,,鼠标右键自己插入插入YAML front Fmatter,在里面添加的字段就行,参数含义对照官网配置文档说明去看.
  >
  > ```
  > title: 测试4
  > author: 小蝌蚪
  > top: false  //是否置顶
  > cover: false
  > toc: true
  > mathjax: false
  > tags:  //标签
  >   - test
  > categories:  //分类
  >   - default
  > date: 2021-01-19 16:05:12
  > img:
  > coverImg:
  > password:  //密码,查了下,功能还没搞定
  > summary: 这里是文章摘要哦.
  > ```
  >
  > 

##### 添加标签/分类设置

- 方法一, Diary1是Diary2的父级

```
categories:
  - Diary1
  - Diary2
tags:
  - PS3
  - Games
```

- 方法二, PlayStation和Games同级,Diary是他们两个的父级, Diary和life同级

```
categories:
  - [Diary, PlayStation]
  - [Diary, Games]
  - [Life]
```

