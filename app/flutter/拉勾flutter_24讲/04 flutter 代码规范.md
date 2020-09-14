## 04 flutter 代码规范

##### 命名规范

- AaBb 类规范，首字母大写驼峰命名法，例如 IsClassName，常用于类的命名。
- aaBb 类规范，首字母小写驼峰命名法，例如 isParameterName，常用于常量以及变量命名
- aa_bb 类规范，小写字母下划线连接法，例如 is_a_flutter_file_name，常用于文件及文件夹命名。

#### 注释规范

- 单行注释: //

- 多行注释:   

  ```
    ///
    /**......* /
    /*......*/ 
  ```

#### 通过注释生成文档

先配置dart环境变量,然后打开命令行工具进入当前项目，或者在 Android Studio 点击界面上的 Terminal 打开命令行窗口，运行如下命令。

```
dartdoc
```

运行结束后，会在当前项目目录生成一个 doc 的文件夹。在生成文件夹中，可以直接打开 doc/api/index.html 文件

#### 代码格式化

[dartfmt](https://github.com/dart-lang/dart_style/wiki/Formatting-Rules)
dartfmt 工具的规范包括了以下几点：

使用空格而不是 tab；

在一个完整的代码逻辑后面使用空行区分；

二元或者三元运算符之间使用空格；

在关键词 , 和 ; 之后使用空格；

一元运算符后请勿使用空格；

在流控制关键词，例如 for 和 while 后，使用空格区分；

在 ( [ { } ] ) 符号后请勿使用空格；

在 { 后前使用空格；

使用 . 操作符，从第二个 . 符号后每次都使用新的一行。

#### 工具化

在 Dart 项目根目录下新增analysis_options.yaml 文件，然后在文件中按照规范填写你需要执行的规则检查即可，目前现有的检查规则可以参考 [Dart linter rules](https://dart-lang.github.io/linter/lints/) 规范.

为了方便，我们可以使用现成已经配置好的规范模版，这里有两个库 [pedantic](https://s0pub0dev.icopy.site/packages/pedantic) 和 [effective_dart](https://s0dart0dev.icopy.site/guides/language/effective-dart) 可以参照使用

> 1. 先项目配置文件（ pubspec.yaml ）中添加依赖:  **pedantic: ^1.9.2**