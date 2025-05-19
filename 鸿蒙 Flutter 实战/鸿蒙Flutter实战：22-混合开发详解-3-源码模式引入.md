## 引言

在前面的文章[混合开发详解-2-Har包模式引入](./鸿蒙Flutter实战：22-混合开发详解-2-Har包模式引入.md)中，我们介绍了如何将 Flutter 模块打包成 Har 包，并引入到原生鸿蒙工程中。本文中，我们将介绍如何通过源码依赖的方式，将 Flutter 模块引入到原生鸿蒙工程中。

## 源码依赖

```bash
# 将 flutter_module 拷贝到 ohos_app 中
cp -r my_flutter_module/.ohos/flutter_module ohos_app/
# 在ohos_app/flutter_module 中创建 rawfile 目录
mkdir -p ohos_app/flutter_module/src/main/resources/rawfile
# 将 flutter 生成的 flutter_module中的flutter_assets 复制到 ohos_app
cp -r my_flutter_module/.ohos/flutter_module/src/main/resources/rawfile/flutter_assets ohos_app/flutter_module/src/main/resources/rawfile
# 将 flutter 生成的中的flutter.har 复制到 ohos_app/har
cp flutter_module/.ohos/har/flutter.har ohos_app/har/flutter.har
```



## 参考资料

- [撰写双端平台代码（插件编写实现）](https://docs.flutter.cn/platform-integration/platform-channels/)
- [鸿蒙Flutter功能开发](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/README.md)
- [鸿蒙add-to-app示例](https://github.com/0xZOne/ohos-flutter-add2app)
- [如何使用混合开发 module](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%B7%B7%E5%90%88%E5%BC%80%E5%8F%91%20module.md)