# 鸿蒙Flutter实战：混合开发

鸿蒙Flutter混合开发主要有两种形式。

## 1.基于har

将flutter module打包成har包，在原生鸿蒙项目中，以har包的方式引入。

其优点是主项目开发者可以不关注Flutter实现，不需要安装配置Flutter开发环境，缺点是无法及时修改Flutter代码，也不存在热重载。

## 2.基于源码

通过源码依赖的当时，在原生鸿蒙项目处，引入Flutter模块。

其优点是方便维护和更新Flutter代码，也可以使用热重载。缺点是需要搭建Flutter开发环境，开发人员需要掌握Flutter。

其项目结构类似如下：

```bash
.
├── AppScope
│   ├── app.json5
│   └── resources
│       ├── base
│       └── rawfile
├── build-profile.json5
├── dependencies
│   ├── hvigor-4.1.1.tgz
│   ├── hvigor-ohos-arkui-x-plugin-3.1.0.tgz
│   └── hvigor-ohos-plugin-4.1.1.tgz
├── entry
│   ├── build-profile.json5
│   ├── hvigorfile.ts
│   ├── oh-package.json5
│   └── src
│       └── main
├── flutter_module
│   ├── BuildProfile.ets
│   ├── Index.ets
│   ├── build-profile.json5
│   ├── consumer-rules.txt
│   ├── hvigorfile.ts
│   ├── libs
│   │   └── arm64-v8a
│   ├── obfuscation-rules.txt
│   ├── oh-package.json5
│   └── src
│       ├── main
│       ├── ohosTest
│       └── test
├── har
│   ├── GT-HM-1.0.4.har
│   ├── flutter.har
│   ├── flutter_boost.har
│   ├── flutter_module.har
│   └── lib_base.har
├── hvigor
│   └── hvigor-config.json5
├── hvigorfile.ts
├── local.properties
├── oh-package.json5
├── package-lock.json
└── package.json
```

## 参考资料

- [撰写双端平台代码（插件编写实现）](https://docs.flutter.cn/platform-integration/platform-channels/)
- [鸿蒙Flutter功能开发](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/README.md)
- [鸿蒙add-to-app示例](https://github.com/0xZOne/ohos-flutter-add2app)
- [如何使用混合开发 module](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%B7%B7%E5%90%88%E5%BC%80%E5%8F%91%20module.md)