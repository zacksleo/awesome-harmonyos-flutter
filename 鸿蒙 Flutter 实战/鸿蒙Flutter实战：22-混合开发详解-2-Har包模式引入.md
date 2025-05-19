# 以 Har 包的方式加载到 HarmonyOS 工程

## 创建 Flutter 模块

首先创建一个 Flutter 模块，我们选择与 ohos 项目同级目录

```bash
flutter create --template=module flutter_module
```

命令行出现以下输出：

```bash
Creating project flutter_module...
Resolving dependencies in `flutter_module`... (1.0s)
Downloading packages...
Got dependencies in `flutter_module`.
Wrote 12 files.

All done!
Your module code is in flutter_module/lib/main.dart.
```

创建 Flutter 模块成功之后，目录结构如下：


![alt text](<截屏2025-05-10 12.43.32.png>)

```bash

```

可以看到，我们将 Flutter 模块放在了与 ohos 项目同级。flutter_module 中自动创建了 .ohos 目录。

##  将 Flutter 模块打包成 Har 包

接下来，我们使用 flutter build har 命令将 Flutter 模块打包成 Har 包。


首先配置签名，用 DevEco 打开 .ohos 目录，然后地项目签名，操作如下：

```
DevEco Studio打开ohos工程后配置调试签名(File -> Project Structure -> Signing Configs 勾选Automatically generate signature)
```

```bash
flutter build har --debug
```

命令行出现以下输出：

```bash
Flutter assets will be downloaded from https://storage.flutter-io.cn. Make sure you trust this source!
Downloading ohos-arm64 tools...                                  2,320ms
Downloading ohos-arm64-profile tools...                          1,043ms
Downloading ohos-arm64-release tools...                            558ms
Running Hvigor task assembleHar...                                 40.1s

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

观察目录 flutter_module/.ohos/har 目录，可以看到 Flutter 模块的 Har 包已经生成了, 里面生成了两个文件，分别是 flutter_module.har 和 flutter.har。

接下来，我们将生成的har包复制到宿主项目 ohos 中，然后回到 ohos 项目工程，将上面生成的 Har 包添依赖配置中。

1. 复制 Har 包

```bash
cp -r my_flutter_module/.ohos/har/* MyApplication/har/
```

 2. 编辑 ohos_app/oh-package.json5 文件：

```json
  "dependencies": {
    "@ohos/flutter_module": "file:har/flutter_module.har",
    "@ohos/flutter_ohos": "file:har/flutter.har"
  },
    "overrides" {
      "@ohos/flutter_ohos": "file:har/flutter.har",
    }
```

这里需要配置 overrides ，为了解决依赖冲突问题，因为 `@ohos/flutter_module`依赖了 `@ohos/flutter_ohos`, 但因为使用的是相对目录，会导致加载失败，故这里通过 overrides 来重新指定  `@ohos/flutter_ohos` 的路径。

另外，与上文提示或者官方文档中不同的是，我们在 dependencies 也添加了 `@ohos/flutter_ohos` ，这是为了 IDE 提示的问题，不加的话会出现以下错误信息

```bash
Cannot find module '@ohos/flutter_ohos' or its corresponding type declarations. <ArkTSCheck>
```

最后, 再次对ohos 项目签名，并运行 DevEco 项目。

## 参考资料

- [flutter_page_sample1](https://gitcode.com/openharmony-sig/flutter_samples/tree/master/ohos/flutter_page_sample1)
- [flutter_page_sample2](https://gitcode.com/openharmony-sig/flutter_samples/tree/master/ohos/flutter_page_sample2)