## 引言

在前面的文章[混合开发详解-2-Har包模式引入](./鸿蒙Flutter实战：22-混合开发详解-2-Har包模式引入.md)中，我们介绍了如何将 Flutter 模块打包成 Har 包，并引入到原生鸿蒙工程中。本文中，我们将介绍如何通过源码依赖的方式，将 Flutter 模块引入到原生鸿蒙工程中。

## 源码依赖

### 模块调整

```bash
# 将 flutter_module 拷贝到 ohos_app 中
cp -r my_flutter_module/.ohos/flutter_module ohos_app/
# 将 flutter 生成的中的flutter.har 复制到 ohos_app/har
cp my_flutter_module/.ohos/har/flutter.har ohos_app/har/flutter.har
```

上面这是一种方式，如果不想使用复制粘贴，也可以使用软链接的方式，将 flutter_module 软链接到 ohos_app 中。

```bash
rm -rf .ohos
ln -s ../ohos_app .ohos
```

链接完以后，再次运行 `flutter run`, 便可以更新鸿蒙宿主工程中的 flutter_module 中的代码，如生成  flutter.har 文件


### 依赖配置

1. 修改 `ohos_app/build-profile.json5` 文件，在 modules 目录下添加模块配置：

```json
  "modules": [
    ...
    {
      "name": "flutter_module",
      "srcPath": "./flutter_module",
      "targets": [
        {
          "name": "default",
          "applyToProducts": [
            "default"
          ]
        }
      ]
    }
  ]
```

2. 编辑 `ohos_app/oh-package.json` 文件，添加如下依赖

```json
  "dependencies": {
    "@ohos/flutter_module": "./flutter_module",
    "@ohos/flutter_ohos": "./har/flutter.har"
  },
```

注意，与 Har 包引入方式不同的是，这里 "@ohos/flutter_module" 引用的是目录，同时 "@ohos/flutter_ohos" 通过相对目录引用 Flutter 引擎库的 Har 包文件。


## 运行项目

回到 DevEco，运行 ohos_app 项目。

## 参考资料

- [撰写双端平台代码（插件编写实现）](https://docs.flutter.cn/platform-integration/platform-channels/)
- [鸿蒙Flutter功能开发](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/README.md)
- [鸿蒙add-to-app示例](https://github.com/0xZOne/ohos-flutter-add2app)
- [如何使用混合开发 module](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%B7%B7%E5%90%88%E5%BC%80%E5%8F%91%20module.md)