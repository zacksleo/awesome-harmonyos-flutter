## 准备工作


### Dart 调用 JS

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