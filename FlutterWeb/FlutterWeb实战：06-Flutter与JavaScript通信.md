## 准备工作

### Dart 调用 JS

这里面我们使用 `js` 库来实现 JS 调用 Dart，首先添加依赖：

```yaml
  dependencies:
+   js: ^0.6.4
```

在 Dart 侧定义调用方法

```dart
@JS()
@anonymous
class WxConfigOption {
  /// 开启调试模式,调用的所有 api 的返回值会在客户端 alert 出来，若要查看传入的参数，可以在 pc 端打开，参数信息会通过 log 打出，仅在 pc 端时才会打印。
  external bool? debug;

  /// 公众号的唯一标识
  external String appId;

  /// 生成签名的时间戳
  external num timestamp;

  /// 生成签名的随机串
  external String nonceStr;

  /// 签名
  external String signature;

  /// 需要使用的 JS 接口列表
  external List<String> jsApiList;

  /// 需要跳转的标签类型
  external List<String> openTagList;

  external factory WxConfigOption({
    bool? debug,
    required String appId,
    required num timestamp,
    required String nonceStr,
    required String signature,
    required List<String> jsApiList,
    required List<String> openTagList,
  });
}
```

```dart
@JS()
class Promise<T> {
  external Promise(void Function(void Function(T result) resolve, Function reject) executor);
  external Promise then(void Function(T result) onFulfilled, [Function onRejected]);
}

/// 声明调用方法
@JS("flutterWeb")
class FlutterWeb {
  /// 配置js-sdk
  external static Promise<void> configJsSdk(WxConfigOption options);

  /// 上传图片
  external static Promise<String> uploadImage();
}

```

在《FlutterWeb实战：04-集成微信JS-SDK提供丰富体验》中，我们介绍了如何封装微信的 JS-SDK 方法，供 Flutter 调用。

最后在 JS 侧导出了被调用方法：

``js
import * as flutterWeb from "./index.js";

window.flutterWeb = flutterWeb;
```

这样就可以在 Dart 侧调用 JS 方法了：

```dart
Future resolveSdkSign() {
  final completer = Completer<void>();
  FlutterWeb.configJsSdk(WxConfigOption(
    appId: appId,
    timestamp: timestamp,
    nonceStr: nonceStr,
    signature: signature,
    jsApiList: ['chooseImage','uploadImage'],
    openTagList: [
        'wx-open-launch-app',
        'wx-open-launch-weapp'
      ]
    )).then(allowInterop(completer.complete),
          allowInterop(completer.completeError));
  return completer.future;
}
```
可以以这种形式调用:

配置 JS-SDK

```dart
resolveSdkSign().then((_) {})
```

上传图片

```dart
FlutterWeb.uploadImage().then(allowInterop(completer.complete),
        allowInterop(completer.completeError));
```


### JS 调用 Dart

```javascript
window.jsOnEvent("events.page.active");
```

```dart
@JS('jsOnEvent')
external set _jsOnEvent(void Function(dynamic event) f);

class PlatformCallWebPlugin {
  static void registerWith(Registrar registrar) {
    final MethodChannel channel = MethodChannel(
        'nicestwood.com/forest', const StandardMethodCodec(), registrar);
    channel.setMethodCallHandler(handleMethodHandler);

    //Sets the call from JavaScript handler
    _jsOnEvent = allowInterop((dynamic event) {
      //
      if (event == 'events.page.active') {
        //  do something
      }
    });
  }
}

```


## 参考资料

- [web-view](https://developers.weixin.qq.com/miniprogram/dev/component/web-view.html)