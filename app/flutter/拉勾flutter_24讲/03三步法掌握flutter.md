![image](https://s0.lgstatic.com/i/image/M00/21/1E/CgqCHl7pvuqAfnGbAAC2vjxyHVc400.png)

#### flutter目录结构讲解

* .idea 

> 这个和 Flutter 无关，这里面主要是保留代码的修改历史。

* android

> 这个目录主要是和 Android 原生平台交互的工程代码，其目录结构和原生的 Android 项目基本一致，但是一些配置和代码结构是不同的。

* ios

> 这个目录主要也是和 iOS 原生平台交互的代码。

- lib


> 这个目录下的文件为 Flutter 项目核心代码，其中包含了一个 main.dart 入口文件。

- test


> 这个目录下的文件存放 Flutter 项目相关的测试文件。

- pubspec.yaml


> 该文件为 Flutter 项目配置文件，包括了项目名、项目描述、版本、运行环境以及开发和正式环境的第三方库，该文件与我们熟悉的 package.json 作用是类似的。

- pubspec.lock


> 这是自动生成的文件，里面指明了 pubspec.yaml 等依赖包和项目依赖库的具体版本号，该文件的功能和我们常见的 package.lock.json 作用类似。

- .metadata


> 这是自动生成的文件，里面记录了项目的属性信息。用于切换分支、升级 SDK 使用。

- .packages


> 这里面放置了项目依赖的库，对应在本机电脑上的绝对路径，为自动生成文件。如果项目出错或者无法找到某个库，可以把这个文件删除，重新自动配置即可。
>

.gitignore、README.md 与前端项目中的文件作用是一致的，这里就不详加说明。

