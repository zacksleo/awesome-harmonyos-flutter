# 以 Har 包的方式加载到 HarmonyOS 工程

## 创建 Flutter 模块

首先创建一个 Flutter 模块，我们选择与 ohos_app 项目同级目录

```bash
flutter create --template=module my_flutter_module
```

命令行出现以下输出：

```bash
Creating project my_flutter_module...
Resolving dependencies in `my_flutter_module`...
Downloading packages...
Got dependencies in `my_flutter_module`.
Wrote 12 files.

All done!
Your module code is in my_flutter_module/lib/main.dart.
```

创建 Flutter 模块成功之后，目录结构如下：


![alt text](image-20.png)


可以看到，我们将 Flutter 模块放在了与 ohos_app 项目同级。my_flutter_module 中自动创建了 .ohos 目录, 这也是一个简单的鸿蒙项目。

##  将 Flutter 模块打包成 Har 包

接下来，我们使用 `flutter build har` 命令将 Flutter 模块打包成 Har 包。

打包前首先配置签名，用 DevEco 打开 .ohos 目录，然后对项目签名，操作如下：

```
DevEco Studio 打开 my_flutter_module/.ohos 工程后配置调试签名(File -> Project Structure -> Signing Configs 勾选 Automatically generate signature)
```

```bash
flutter build har --debug
```

命令行出现以下输出：

```bash
Running Hvigor task assembleHar...                                 47.5s

Consuming the Module
    1. Open <host project>/oh-package.json5
    2. Add flutter_module to the dependencies list:

      "dependencies": {
        "@ohos/flutter_module": "file:path/to/har/flutter_module.har"
      }

    3. Override flutter and plugins dependencies:

      "overrides" {
        "@ohos/flutter_ohos": "file:path/to/har/flutter.har",
      }
```

观察目录 `my_flutter_module/.ohos/har` 目录，可以看到 Flutter 模块的 Har 包已经生成了, 里面生成了两个文件，分别是 flutter_module.har 和 flutter.har。

> 注意，生成的  flutter_module.har 是默认名称，与项目名无关。如何想要修改生成的名称，可在 my_flutter_module/.ohos/flutter_module/oh-package.json5  文件中修改包名。

![alt text](image-20.png)

## 引入 Har 包到 ohos 项目中

接下来，我们将生成的 har 包复制到宿主项目 ohos 中，然后回到 ohos 项目工程，将上面生成的 Har 包添依赖配置中。

1. 复制 Har 包

```bash
cp -r my_flutter_module/.ohos/har/* ohos/har/
```

 2. 编辑 ohos_app/oh-package.json5 文件：

```json
  "dependencies": {
    "@ohos/flutter_module": "file:har/my_flutter_module.har",
    "@ohos/flutter_ohos": "file:har/my_flutter.har"
  },
  "overrides" {
    "@ohos/flutter_ohos": "file:har/flutter.har",
  }
```

> 注意：如何不想使用复制Har包的方式，也可以通过相对路径直接引入原Har的位置，可使用以下方式引入：

```json
  "dependencies": {
    "@ohos/flutter_module": "file:../my_flutter_module/.ohos/har/flutter_module.har",
    "@ohos/flutter_ohos": "file:../my_flutter_module/.ohos/har/flutter.har"
  },
  "overrides": {
    "@ohos/flutter_ohos": "file:../my_flutter_module/.ohos/har/flutter.har"
  },
```

这里需要配置 overrides ，为了解决依赖冲突问题，因为 `@ohos/flutter_module`依赖了 `@ohos/flutter_ohos`, 但因为使用的是相对目录，会导致加载失败，故这里通过 overrides 来重新指定  `@ohos/flutter_ohos` 的路径。

另外，与上文提示或者官方文档中不同的是，我们在 dependencies 也添加了 `@ohos/flutter_ohos` ，这是为了 IDE 提示的问题，不加的话会出现以下错误信息

```bash
Cannot find module '@ohos/flutter_ohos' or its corresponding type declarations. <ArkTSCheck>
```

最后, 再次对 ohos 项目签名，并运行 DevEco 项目。

## 接下来

现在我们只是将 Har 包引入到 ohos 项目中，在接下来的文章 [跳转Flutter页面](./鸿蒙Flutter实战：22-混合开发详解-4-跳转Flutter页面.md)中，我们将介绍如何在 ohos 原生项目中，初始化 Flutter 引擎，并在合适的地方跳转打开 Flutter 页面。

## 总结

1. 这种模式适合较大的项目团队，常见的场景是，负责 Flutter 开发的同事开发好指定的模块，以 Har 包的形式交付给鸿蒙原生的开发团队。

2. 在这种模式下，鸿蒙原生的开发团队，不需要太多关注 Flutter 部分的内容，甚至不需要安装 Flutter 开发环境，可以更好的职责分离。

3. 缺点，由于 Flutter 模块打包成了 Har 包，以 so 文件存在，故 Flutter 无法热重载。

## 参考资料

- [flutter_page_sample1](https://gitcode.com/openharmony-sig/flutter_samples/tree/master/ohos/flutter_page_sample1)
- [flutter_page_sample2](https://gitcode.com/openharmony-sig/flutter_samples/tree/master/ohos/flutter_page_sample2)