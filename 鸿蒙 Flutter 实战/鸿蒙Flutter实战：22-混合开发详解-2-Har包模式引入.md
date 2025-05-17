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

接下来，我们将回到 ohos 项目工程，将上面生成的 Har 包添依赖配置中。

编辑 ohos_app/oh-package.json5 文件：

```json
  "dependencies": {
    "@ohos/flutter_module": "file:../flutter_module/.ohos/har/flutter_module.har",
    "ohos/flutter_ohos": "file:../flutter_module/.ohos/har/flutter.har"
  },
```

修改 Ohos 的入口文件, 将 Flutter 模块生成的 .ohos目录中的 EntryAbility.ets 和 Index.ets 文件复制到宿主工程中进行替换

```
cp flutter_module/.ohos/entry/src/main/ets/entryability/EntryAbility.ets ohos_app/entry/src/main/ets/entryability/EntryAbility.ets
cp flutter_module/.ohos/entry/src/main/ets/pages/Index.ets ohos_app/entry/src/main/ets/pages/Index.ets
```

其中 `EntryAbility` 继承自 `FlutterAbility`，而 `FlutterAbility` 继承自 `UIAbility`, 它在 `UIAbility` 上增加了以下功能：

1. Flutter引擎全生命周期管理
  - 初始化： 创建 Flutter引擎
  - 销毁：在 onDestroy 中释放Flutter引擎，避免内存泄漏
  - 状态同步：将宿主 Ability 的生命周期事件，实时同步至 Flutter 引擎， 如 onCreate、onDestroy、onForeground、onBackground 等。




最后运行 DevEco 项目。

## 参考资料

- [flutter_page_sample1](https://gitcode.com/openharmony-sig/flutter_samples/tree/master/ohos/flutter_page_sample1)
- [flutter_page_sample2](https://gitcode.com/openharmony-sig/flutter_samples/tree/master/ohos/flutter_page_sample2)