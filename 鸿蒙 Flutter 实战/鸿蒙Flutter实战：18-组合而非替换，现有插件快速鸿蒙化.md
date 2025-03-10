3. 联合插件方式

除了上面使用 dependency_overrides 来配置鸿蒙适配库的两种方式以外，如果三方插件本身使用了联合插件的形式，也可以通过下面这种方式来添加鸿蒙平台的实现：

```yaml
dependencies:

  image_picker: ^1.1.2
  image_picker_ohos:
    git:
      url: "https://gitcode.com/openharmony-sig/flutter_packages.git"
      path: "packages/image_picker/image_picker_ohos"
```

这种方式称作 ["未整合的联合插件"](https://docs.flutter.cn/packages-and-plugins/developing-packages#non-endorsed-federated-plugin), 在上面的配置中，
image_picker 是一个联合插件, 这里直接使用官方社区的最新版本，观察该插件的 pubspec.yaml 的文件，通过其结构可以发现联合插件的特点, 该插件的依赖项为：

```bash
dependencies:
  image_picker_platform_interface: ^2.10.0
  ...
  image_picker_android: ^0.8.7
  image_picker_ios: ^0.8.8
```

`image_picker_platform_interface` 是一个抽象层，它定义了平台相关的接口，下面是各个平台的实现，通过拆分成包，以依赖的方式加载，那同样的原理，就可以再添加一条鸿蒙平台的实现包，就可以完成鸿蒙化适配, 也就是上面案例中的 `image_picker_ohos`：

```yaml
  image_picker_ohos:
    git:
      url: "https://gitcode.com/openharmony-sig/flutter_packages.git"
      path: "packages/image_picker/image_picker_ohos"
```