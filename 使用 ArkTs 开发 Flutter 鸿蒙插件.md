# 使用 ArkTs 开发 Flutter 鸿蒙平台插件

本文讲述如何开发一个 Flutter 鸿蒙插件，如何实现 Flutter 与鸿蒙的混合开发，以及双端消息通信。

## Flutter侧，编写 MethodChannel

```dart
const MethodChannel _methodChannel = MethodChannel('xxx.com/app');


  /// 获取token
  static Future<dynamic> getToken() {
    return _methodChannel.invokeMethod("getPrefs", 'token');
  }

  /// 设置token
  static Future<dynamic> setToken(String token) {
    return _methodChannel
        .invokeMethod("setPrefs", {'key': 'token', 'value': token});
  }

```

代码生命了一个 methodChannel, 并实现了 token 存错的调用方法。

## ArkTs侧，实现调用

编写 src/main/ets/entryability/EntryAbility.ets

```ets

import { FlutterAbility, FlutterEngine } from '@ohos/flutter_ohos';
import { GeneratedPluginRegistrant } from '../plugins/GeneratedPluginRegistrant';
import ForestPlugin from './ForestPlugin';
import { BusinessError } from '@kit.BasicServicesKit';
import { window } from '@kit.ArkUI';
import { preferences } from '@kit.ArkData';

let dataPreferences: preferences.Preferences | null = null;

export default class EntryAbility extends FlutterAbility {

  onWindowStageCreate(windowStage: window.WindowStage): void {
    super.onWindowStageCreate(windowStage);
    preferences.getPreferences(this.context, 'forestStore', (err: BusinessError, val: preferences.Preferences) => {
      if (err) {
        console.error("Failed to get preferences. code =" + err.code + ", message =" + err.message);
        return;
      }
      dataPreferences = val;
      console.info("Succeeded in getting preferences1.");
    })
  }

  configureFlutterEngine(flutterEngine: FlutterEngine) {
    super.configureFlutterEngine(flutterEngine)
    GeneratedPluginRegistrant.registerWith(flutterEngine)
    this.addPlugin(new ForestPlugin());
  }
}

export {dataPreferences};
```

该文件使的原生页面在加载时，配置 Flutter 引擎，注册插件。 Flutter初始化时，同时初始化了 首选项dataPreferences，以备后用。

编写 src/main/ets/entryability/ForestPlugin.ets

```ets
import { Any, BasicMessageChannel, EventChannel, FlutterManager, FlutterPlugin, Log, MethodCall, MethodChannel, StandardMessageCodec} from '@ohos/flutter_ohos';
import { FlutterPluginBinding } from '@ohos/flutter_ohos/src/main/ets/embedding/engine/plugins/FlutterPlugin';
import { batteryInfo } from '@kit.BasicServicesKit';
import { MethodCallHandler, MethodResult } from '@ohos/flutter_ohos/src/main/ets/plugin/common/MethodChannel';
import { preferences } from '@kit.ArkData';
import { BusinessError } from '@kit.BasicServicesKit';
import {dataPreferences} from './EntryAbility';
import router from '@ohos.router'
import { webviewRouterParams } from '../pages/Webview';

const TAG = "[flutter][plugin][forest]";

export default class ForestPlugin implements FlutterPlugin {
  private channel?: MethodChannel;
  private basicChannel?: BasicMessageChannel<Any>;
  private api = new ForestApi();
  private dataPreferences: preferences.Preferences | null = null;

  onAttachedToEngine(binding: FlutterPluginBinding): void {
    this.channel = new MethodChannel(binding.getBinaryMessenger(), "nicestwood.com/forest");
    this.channel.setMethodCallHandler({
      onMethodCall : (call: MethodCall, result: MethodResult) => {
        console.log(`${TAG}-->[${call.method}]: ${JSON.stringify(call.args)}`);
        switch (call.method) {
          case "getPrefs":
            this.api.getPrefs(String(call.args), result);
            break;
          case "setPrefs":
            let key = String(call.argument("key"));
            let value = String(call.argument("value"));
            this.api.setPrefs(key, value);
          default:
            result.notImplemented();
            break;
        }
      }
    })
  }
  //···
  onDetachedFromEngine(binding: FlutterPluginBinding): void {
    Log.i(TAG, "onDetachedFromEngine");
    this.channel?.setMethodCallHandler(null);
  }

  getUniqueClassName(): string {
    return "ForestPlugin";
  }
```

以上代码实现了一个插件类，其核心实现了FlutterPlugin中的 onAttachedToEngine方法，该方法在 Flutter 引擎加载成功后调用。

`onMethodCall`中接收来自 Flutter 的消息调用，分别实现了 'getPrefs' 和 'setPrefs' 两个回掉，其中 `getPrefs`有返回值，通过 `result.success(val);`（见下）异步返回，
`setPrefs`没有返回值。

以下为 `ForestApi`的具体实现，使用了 HarmonyOS 中的首选项 API 设置和读取数据。

```ets
class ForestApi {
  getPrefs(key: string, result: MethodResult) {
    dataPreferences?.get(key, '', (err: BusinessError, val: preferences.ValueType) => {
      if (err) {
        console.error(`${TAG} Failed to get value of ${key}. code =` + err.code + ", message =" + err.message);
        result.success('');
      }
      console.info(`${TAG} Succeeded in getting value of ${key}:${val}.`);
      result.success(val);
    })

  }

  setPrefs(key: string, value: string) {
    dataPreferences?.put(key, value, (err: BusinessError) => {
      if (err) {
        console.error(`${TAG} Failed to put value of ${key}. code =` + err.code + ", message =" + err.message);
        return;
      }
      console.info(`${TAG} Succeeded in putting value of ${key}.`);
    })
  }

  clearPrefs(key: string) {
    dataPreferences?.delete(key, (err: BusinessError) => {
      if (err) {
        console.error("Failed to delete the key 'startup'. code =" + err.code + ", message =" + err.message);
        return;
      }
      console.info(`Succeeded in deleting the key ${key}.`);
    })
  }
}

```

## 参考资料

- [撰写双端平台代码（插件编写实现）](https://docs.flutter.cn/platform-integration/platform-channels/)
- [用户首选项API](https://developer.huawei.com/consumer/cn/doc/harmonyos-references/js-apis-data-preferences-V5)