## 本文以同层渲染为例，介绍如何集成高德地图

完整代码见 [Flutter 鸿蒙版 Demo](https://gitee.com/zacks/flutter-ohos-demo/commit/2b16c6f34abd4c61eea89805bc314a10874c305f)

## 概述

## Dart 侧

核心代码如下，通过 OhosView 来承载原生视图

```dart
OhosView(
      viewType: 'com.shaohushuo.app/customView',
      onPlatformViewCreated: _onPlatformViewCreated,
      creationParams: const <String, dynamic>{'initParams': 'hello world'},
      creationParamsCodec: const StandardMessageCodec(),
    )
```

其中 viewType 为自定义的 ohosView 的名称，onPlatformViewCreated 为创建完成回调，creationParams 为创建时传入的参数，creationParamsCodec 为参数编码格式。


### ArkTS 侧

这里面我们按照《如何使用PlatformView》中的示例操作，首先需要创建一个显示高德地图的视图，其核心代码如下：


完整文件 [AmapWidgetFactory.ets](https://gitee.com/zacks/flutter-ohos-demo/blob/master/packages/apps/ohos_app/ohos/entry/src/main/ets/entryability/AmapWidget/AmapWidgetView.ets)

```dart

MapsInitializer.setApiKey("e4147e927a1f63a0acff45cecf9419b5");
MapViewManager.getInstance().registerMapViewCreatedCallback((mapview?: MapView, mapViewName?: string) => {
  if (!mapview) {
    return;
  }
  let mapView = mapview;
  mapView.onCreate();
  mapView.getMapAsync((map) => {
    let aMap: AMap = map;
  })
})

@Component
struct ButtonComponent {
  @Prop params: Params
  customView: AmapWidgetView = this.params.platformView as AmapWidgetView

  build() {
    Row() {
      MapViewComponent().width('100%').height('100%')
    }
  }
}
```

接下来创建一个 [AmapWidgetFactory.ets](https://gitee.com/zacks/flutter-ohos-demo/blob/master/packages/apps/ohos_app/ohos/entry/src/main/ets/entryability/AmapWidget/AmapWidgetFactory.ets)

```dart
export class AmapWidgetFactory extends PlatformViewFactory {
  message: BinaryMessenger;

  constructor(message: BinaryMessenger, createArgsCodes: MessageCodec<Object>) {
    super(createArgsCodes);
    this.message = message;
  }

  public create(context: common.Context, viewId: number, args: Object): PlatformView {
    return new AmapWidgetView(context, viewId, args, this.message);
  }
}
```

最终需要创建一个 [AmapWidgetPlugin.ets](https://gitee.com/zacks/flutter-ohos-demo/blob/master/packages/apps/ohos_app/ohos/entry/src/main/ets/entryability/AmapWidget/AmapWidgetPlugin.ets)


```dart
export class AmapWidgetPlugin implements FlutterPlugin {
  getUniqueClassName(): string {
    return 'AmapWidgetPlugin';
  }

  onAttachedToEngine(binding: FlutterPluginBinding): void {
    binding.getPlatformViewRegistry()?.
    registerViewFactory('com.shaohushuo.app/customView', new AmapWidgetFactory(binding.getBinaryMessenger(), StandardMessageCodec.INSTANCE));
  }

  onDetachedFromEngine(binding: FlutterPluginBinding): void {}
}
```

插件创建好之后，记得在 EntryAbility 中注册插件

```dart
 this.addPlugin(new AmapWidgetPlugin())
```

> 需要注意的是，视图ID一定要两侧保持一致，如这里名为 'com.shaohushuo.app/customView'，否则无法正常显示

## 截图

![alt text](figures/20-amap.png)

## 参考资料

- [如何使用PlatformView](https://gitcode.com/openharmony-sig/flutter_samples/blob/br_3.7.12-ohos-1.0.5/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8PlatformView.md)
- [PlatformView同层渲染新方案](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/PlatformView%E5%90%8C%E5%B1%82%E6%B8%B2%E6%9F%93%E6%96%B9%E6%A1%88%E9%80%82%E9%85%8D%E5%88%87%E6%8D%A2%E6%8C%87%E5%AF%BC.md#platformview%E6%96%B0%E6%96%B9%E6%A1%88)