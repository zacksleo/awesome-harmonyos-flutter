# 鸿蒙 Flutter 开发中集成 Webview

## 主要有两种方案

### 使用第三方库

如 使用`flutter_inappwebview`插件，在 pubspec.lock 文件中配置：

```yaml
  flutter_inappwebview:
    git:
      url: https://gitcode.com/openharmony-sig/flutter_inappwebview.git
      path: "flutter_inappwebview"
```

或者使用 webview_flutter 插件

```yaml
  webview_flutter:
    git:
      url: https://gitcode.com/openharmony-sig/flutter_packages.git
      path: "packages/webview_flutter/webview_flutter"
```

### 编写原生 ArkTS 代码实现 PlatformView

创建 entryablitiy

在 src/main/module.json5中配置ablitiy

```json
    "abilities": [
      {
        "name": "EntryAbility",
        "srcEntry": "./ets/entryability/EntryAbility.ets",
        "description": "$string:EntryAbility_desc",
        "icon": "$media:icon",
        "label": "$string:EntryAbility_label",
        "startWindowIcon": "$media:icon",
        "startWindowBackground": "$color:start_window_background",
        "exported": true,
        "skills": [
          {
            "entities": [
              "entity.system.home"
            ],
            "actions": [
              "action.system.home"
            ]
          }
        ],
        "orientation": "landscape"
      }
    ],
```

`cat src/main/entryablity/CustomFactory.ets`

```ets
import { BinaryMessenger } from '@ohos/flutter_ohos/src/main/ets/plugin/common/BinaryMessenger';
import MessageCodec from '@ohos/flutter_ohos/src/main/ets/plugin/common/MessageCodec';
import PlatformViewFactory from '@ohos/flutter_ohos/src/main/ets/plugin/platform/PlatformViewFactory';
import { CustomView } from './CustomView';
import common from '@ohos.app.ability.common';
import PlatformView from '@ohos/flutter_ohos/src/main/ets/plugin/platform/PlatformView';

export class CustomFactory extends PlatformViewFactory {
  message: BinaryMessenger;

  constructor(message: BinaryMessenger, createArgsCodes: MessageCodec<Object>) {
    super(createArgsCodes);
    this.message = message;
  }

  public create(context: common.Context, viewId: number, args: Object): PlatformView {
    return new CustomView(context, viewId, args, this.message);
  }
}
```

`cat src/main/entryablity/CustomPlugin.ets`

```ets
import  { FlutterPlugin,
  FlutterPluginBinding } from '@ohos/flutter_ohos/src/main/ets/embedding/engine/plugins/FlutterPlugin';
import StandardMessageCodec from '@ohos/flutter_ohos/src/main/ets/plugin/common/StandardMessageCodec';
import { CustomFactory } from './CustomFactory';

export class CustomPlugin implements FlutterPlugin {
  getUniqueClassName(): string {
    return 'CustomPlugin';
  }

  onAttachedToEngine(binding: FlutterPluginBinding): void {
    binding.getPlatformViewRegistry()?.
    registerViewFactory('com.rex.custom.ohos/customView', new CustomFactory(binding.getBinaryMessenger(), StandardMessageCodec.INSTANCE));
  }

  onDetachedFromEngine(binding: FlutterPluginBinding): void {}
}
```

`cat src/main/entryablity/CustomView.ets`

```ets
import MethodChannel, {
  MethodCallHandler,
  MethodResult
} from '@ohos/flutter_ohos/src/main/ets/plugin/common/MethodChannel';
import PlatformView, { Params } from '@ohos/flutter_ohos/src/main/ets/plugin/platform/PlatformView';
import common from '@ohos.app.ability.common';
import { BinaryMessenger } from '@ohos/flutter_ohos/src/main/ets/plugin/common/BinaryMessenger';
import StandardMethodCodec from '@ohos/flutter_ohos/src/main/ets/plugin/common/StandardMethodCodec';
import MethodCall from '@ohos/flutter_ohos/src/main/ets/plugin/common/MethodCall';
import { webview } from '@kit.ArkWeb';

@Component
struct ButtonComponent {
  @Prop params: Params
  customView: CustomView = this.params.platformView as CustomView
  @StorageLink('numValue') storageLink: string = "first"
  @State bkColor: Color = Color.Red

  private webviewController: WebviewController = new webview.WebviewController()

  build() {
    Web({src: 'https://baidu.com', controller: this.webviewController})
      .domStorageAccess(true)
      .fileAccess(true)
      .mixedMode(MixedMode.All)
      .databaseAccess(true)
      .userAgent("Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.71 Safari/537.36")
  }
}

@Builder
function ButtonBuilder(params: Params) {
  ButtonComponent({ params: params })
    .backgroundColor(Color.Transparent)
}

AppStorage.setOrCreate('numValue', 'test')

@Observed
export class CustomView extends PlatformView implements MethodCallHandler {
  numValue: string = "test";

  methodChannel: MethodChannel;
  index: number = 1;

  constructor(context: common.Context, viewId: number, args: ESObject, message: BinaryMessenger) {
    super();
    // 注册消息通道
    this.methodChannel = new MethodChannel(message, `com.rex.custom.ohos/customView${viewId}`, StandardMethodCodec.INSTANCE);
    this.methodChannel.setMethodCallHandler(this);
  }

  onMethodCall(call: MethodCall, result: MethodResult): void {
    // 接受Dart侧发来的消息
    let method: string = call.method;
    let link1: SubscribedAbstractProperty<number> = AppStorage.link('numValue');
    switch (method) {
      case 'getMessageFromFlutterView':
        let value: ESObject = call.args;
        this.numValue = value;
        link1.set(value)
        console.log("nodeController receive message from dart: " + this.numValue);
        result.success(true);
        break;
    }
  }

  public  sendMessage = () => {
    console.log("nodeController sendMessage")
    //向Dart侧发送消息
    this.methodChannel.invokeMethod('getMessageFromOhosView', 'natvie - ' + this.index++);
  }

  getView(): WrappedBuilder<[Params]> {
    return new WrappedBuilder(ButtonBuilder);
  }

  dispose(): void {
  }
}
```

`cat src/main/entryablity/EntryAbility.ets`

```ets
import { FlutterAbility, FlutterEngine } from '@ohos/flutter_ohos';
import { GeneratedPluginRegistrant } from '../plugins/GeneratedPluginRegistrant';
import { CustomPlugin } from './CustomPlugin';

export default class EntryAbility extends FlutterAbility {
  configureFlutterEngine(flutterEngine: FlutterEngine) {
    super.configureFlutterEngine(flutterEngine)
    GeneratedPluginRegistrant.registerWith(flutterEngine)
    this.addPlugin(new CustomPlugin())
  }
}

```

创建 pages

cat src/main/ets/pages/index.ets

```ets
import common from '@ohos.app.ability.common';
import { FlutterPage } from '@ohos/flutter_ohos'

let storage = LocalStorage.getShared()
const EVENT_BACK_PRESS = 'EVENT_BACK_PRESS'

@Entry(storage)
@Component
struct Index {
  private context = getContext(this) as common.UIAbilityContext
  @LocalStorageLink('viewId') viewId: string = "";

  build() {
    Column() {
      FlutterPage({ viewId: this.viewId })
    }
  }

  onBackPress(): boolean {
    this.context.eventHub.emit(EVENT_BACK_PRESS)
    return true
  }
}
```

在src/main/resources/base/profile/main_page.json中配置路由

```json
{
  "src": [
    "pages/Index"
  ]
}
```

在 Dart 侧调用该 PlatformView

```dart
Scaffold(
  appBar: AppBar(title: Text('code')),
  body: OhosView(
  viewType: 'com.rex.custom.ohos/customView',
  // onPlatformViewCreated: _onPlatformViewCreated,
  creationParams: const <String, dynamic>{'initParams': 'hello world'},
  creationParamsCodec: const StandardMessageCodec(),
)
```

## 参考资料

- [flutter_inappwebview](https://gitcode.com/openharmony-sig/flutter_inappwebview)
- [如何使用PlatformView](https://gitcode.com/openharmony-sig/flutter_samples/blob/master/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8PlatformView.md)
