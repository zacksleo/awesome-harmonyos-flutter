## 配置

将 Flutter 模块添加至宿主鸿蒙项目中后，接下需要实现页面跳转、消息通信等功能，本文重点介绍如何跳转至 Flutter 页面。


## 项目配置


### 添加依赖

编辑 ohos_app/oh-package.json 文件

1. 如果通过 Har 包方式引入 Flutter 模块，则需要添加如下内容

```diff
  "dependencies": {
+    "@ohos/flutter_module": "file:har/my_flutter_module.har",
+    "@ohos/flutter_ohos": "file:har/my_flutter.har"
  },
  "overrides" {
+    "@ohos/flutter_ohos": "file:har/flutter.har",
  }
```

2. 如果通过源码方式引入 Flutter 模块，则需要添加如下内容：

```diff
  "dependencies": {
+    "@ohos/flutter_module": "./flutter_module",
+    "@ohos/flutter_ohos": "./har/flutter.har"
  },
```

### Flutter 引擎配置

编辑 `ohos_app/entry/src/main/ets/entryability/EntryAbility.ts` 文件，按以方式修改：

```diff
-import { AbilityConstant, ConfigurationConstant, UIAbility, Want } from '@kit.AbilityKit';
-import { hilog } from '@kit.PerformanceAnalysisKit';
-import { window } from '@kit.ArkUI';
+import { FlutterAbility, FlutterEngine } from '@ohos/flutter_ohos';
+import { GeneratedPluginRegistrant } from '@ohos/flutter_module';

-const DOMAIN = 0x0000;
-
-export default class EntryAbility extends UIAbility {
-  onCreate(want: Want, launchParam: AbilityConstant.LaunchParam): void {
-    this.context.getApplicationContext().setColorMode(ConfigurationConstant.ColorMode.COLOR_MODE_NOT_SET);
-    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onCreate');
-  }
-
-  onDestroy(): void {
-    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onDestroy');
-  }
-
-  onWindowStageCreate(windowStage: window.WindowStage): void {
-    // Main window is created, set main page for this ability
-    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onWindowStageCreate');
-
-    windowStage.loadContent('pages/Index', (err) => {
-      if (err.code) {
-        hilog.error(DOMAIN, 'testTag', 'Failed to load the content. Cause: %{public}s', JSON.stringify(err));
-        return;
-      }
-      hilog.info(DOMAIN, 'testTag', 'Succeeded in loading the content.');
-    });
-  }
-
-  onWindowStageDestroy(): void {
-    // Main window is destroyed, release UI related resources
-    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onWindowStageDestroy');
-  }
-
-  onForeground(): void {
-    // Ability has brought to foreground
-    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onForeground');
-  }
-
-  onBackground(): void {
-    // Ability has back to background
-    hilog.info(DOMAIN, 'testTag', '%{public}s', 'Ability onBackground');
+export default class EntryAbility extends FlutterAbility {
+  configureFlutterEngine(flutterEngine: FlutterEngine) {
+    super.configureFlutterEngine(flutterEngine)
+    GeneratedPluginRegistrant.registerWith(flutterEngine);
   }
}
```

这里面的作用是配置 Flutter 引擎，注册插件。

