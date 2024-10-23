# 鸿蒙Flutter 实战：使用第三方插件

在鸿蒙Flutter开发中，如果涉及到使用原生功能，就要使用插件。使用插件有两种方式，一种是自己编写原生ArkTS代码，在Dart侧调用。另外一种是使用第三方代码。

## 方式一：编号原生 ArkTS 代码

该方案可以使用 PlatformView 或者 MethodChannel 调用。

1. PlatformView 即为在 Flutter 侧创建一个 View，然后在 Native 侧渲染。PlatformView 封装了底层的 View。

2. MethodChannel 即通过 MethodClannel 调用原生Native 方法。

具体操作可以分别参考文章 [鸿蒙 Flutter 开发中集成 Webview](https://gitee.com/zacks/awesome-harmonyos-flutter/blob/master/%E9%B8%BF%E8%92%99Flutter%E5%AE%9E%E6%88%98%EF%BC%9A03-%E9%B8%BF%E8%92%99Flutter%E5%BC%80%E5%8F%91%E4%B8%AD%E9%9B%86%E6%88%90Webview.md) 和 [使用 ArkTs 开发 Flutter 鸿蒙平台插件](https://gitee.com/zacks/awesome-harmonyos-flutter/blob/master/%E9%B8%BF%E8%92%99Flutter%E5%AE%9E%E6%88%98%EF%BC%9A06-%E4%BD%BF%E7%94%A8ArkTs%E5%BC%80%E5%8F%91Flutter%E9%B8%BF%E8%92%99%E6%8F%92%E4%BB%B6.md)

## 方式二：使用第三方代码

1.在pub.flutter.dev/github/gitee/ophm查找使用的插件，如果插件已经适配鸿蒙，则可以像其他Flutter插件一样正常使用。

2.如果插件尚未适配鸿蒙，则需要寻找适配的插件库。配置方法如下

3.如果使用的第三方插件，其底层以的库没有适配鸿蒙，则需要通过overrider配置其鸿蒙化的替代插件，否则会在运行时报错。如下面所示：

```yaml
dependency_overrides:
  # ohos
  path_provider:
    git:
      url: "https://gitee.com/openharmony-sig/flutter_packages.git"
      path: "packages/path_provider/path_provider"
```

另外，如果没有找到使用的鸿蒙化插件，则可以考虑自行编写垮端调用代码，或者编写新的插件库，作为原插件库的特定平台实现。

## 参考资料

- [如何使用PlatformView](https://gitee.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8PlatformView.md)
- [使用 ArkTs 开发 Flutter 鸿蒙平台插件](https://gitee.com/zacks/awesome-harmonyos-flutter/blob/master/%E9%B8%BF%E8%92%99Flutter%E5%AE%9E%E6%88%98%EF%BC%9A06-%E4%BD%BF%E7%94%A8ArkTs%E5%BC%80%E5%8F%91Flutter%E9%B8%BF%E8%92%99%E6%8F%92%E4%BB%B6.md)