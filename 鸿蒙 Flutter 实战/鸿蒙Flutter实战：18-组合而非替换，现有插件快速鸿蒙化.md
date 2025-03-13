## 引言

在对插件鸿蒙化时，除了往期文章[现有Flutter项目支持鸿蒙II](https://gitee.com/zacks/flutter-ohos-demo)中讲到的使用 dependency_overrides 来配置鸿蒙适配库的两种方式以外，如果三方插件本身使用了联合插件的形式，也可以通过下面这种方式来添加鸿蒙平台的实现：

```yaml
dependencies:

  image_picker: ^1.1.2
  image_picker_ohos:
    git:
      url: "https://gitcode.com/openharmony-sig/flutter_packages.git"
      path: "packages/image_picker/image_picker_ohos"
```

这也是一种非常优雅的方式，不用修改插件的代码，直接在项目配置中增加鸿蒙适配库即可。

## 什么是联合插件？

Federated plugins (联合插件) 是一种将对不同平台的支持分为单独的软件包。所以，联合插件能够使用针对 iOS、Android、Web 甚至是针对汽车 (例如在 IoT 设备上)分别使用对应的 package。除了这些好处之外，它还能够让领域专家在他们最了解的平台上扩展现有平台插件。

联合插件需要以下 package：

### 面向应用的 package
该 package 是用户使用插件的的直接依赖。它指定了 Flutter 应用使用的 API。

### 平台 package
一个或多个包含特定平台代码的 package。面向应用的 package 会调用这些平台 package—— 除非它们带有一些终端用户需要的特殊平台功能，否则它们不会包含在应用中。

### 平台接口 package
将面向应用的 package 与平台 package 进行整合的 package。该 package 会声明平台 package 需要实现的接口，供面向应用的 package 使用。使用单一的平台接口 package 可以确保所有平台 package 都按照各自的方法实现了统一要求的功能。

## 什么是未整合的联合插件？

相对的，整合的联合插件，也就是说插件在某个平台的实现，被整合进了主package，也就是"面向应用的 package"。如果插件已经整合了ohos实现，如 fluwx，则直接使用即可，无需再添加鸿蒙平台的实现。

如果插件没有整合ohos实现，如 image_picker, 则需要添加鸿蒙平台的实现。：

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

## 注意事项

需要注意，并非所有的插件都适合这种方式，有两种情况并不适合：

1.插件并非联合插件，也就是将所有平台的实现聚合在一个package中，这种建议使用 dependence_override 来覆写插件依赖
2.插件虽然是联合插件，但是鸿蒙化需要修改抽象层代码，典型的如用了Platform.ohos 这种只有鸿蒙Flutter开发SDK才有的API，这种也建议使用dependence_override
