## 概述

将 Flutter 模块添加至宿主鸿蒙项目中后，接下需要实现页面跳转、消息通信等功能，本文重点介绍如何初始化 Flutter。


## 项目配置


### 添加依赖

编辑 ohos_app/oh-package.json 文件

1. 如果通过 Har 包方式引入 Flutter 模块，则需要添加如下内容

```json
  "dependencies": {
    "@ohos/flutter_module": "file:har/my_flutter_module.har",
    "@ohos/flutter_ohos": "file:har/my_flutter.har"
  },
  "overrides" {
    "@ohos/flutter_ohos": "file:har/flutter.har",
  }
```

2. 如果通过源码方式引入 Flutter 模块，则需要添加如下内容：

```json
  "dependencies": {
    "@ohos/flutter_module": "./flutter_module",
    "@ohos/flutter_ohos": "./har/flutter.har"
  },
```

## Flutter 引擎初始化

编辑 `ohos_app/entry/src/main/ets/entryability/EntryAbility.ts` 文件，按以下方式修改：

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

最终 EntryAbility.ts 文件内容如下：

```ts
import { FlutterAbility, FlutterEngine } from '@ohos/flutter_ohos';
import { GeneratedPluginRegistrant } from '@ohos/flutter_module';

export default class EntryAbility extends FlutterAbility {
  configureFlutterEngine(flutterEngine: FlutterEngine) {
    super.configureFlutterEngine(flutterEngine)
    GeneratedPluginRegistrant.registerWith(flutterEngine);
  }
}
```

`EntryAbility` 继承自 `FlutterAbility`，而 `FlutterAbility` 继承自 `UIAbility`, 它在 `UIAbility` 上增加了以下功能：


1. 引擎管理
  - 初始化Flutter引擎（FlutterEngine）
  - 通过Delegate处理Flutter与原生能力绑定
  - 管理窗口生命周期(create/destroy)
2. UI交互
  - 创建FlutterView视图容器
  - 处理系统配置变化（深色模式/字体缩放）
  - 实现多语言/无障碍服务适配
3. 生命周期协调
  - 转发原生生命周期事件到Flutter层（onForeground/onBackground）
  - 处理异常恢复（appRecovery.restartApp）
4. 扩展支持
  - 提供插件管理接口（addPlugin）
  - 支持热重载配置同步（onConfigurationUpdate)


## 添加 FlutterPage

在 `entry/src/main/etc/pages` 目录下添加一个鸿蒙页面, 例如名称为 `FlutterContainerPage`，用于承载 FlutterPage,

鼠标右键点击 `ohos/entry/src/main/ets/pages` 目录，依次选择 New->Page->Empty Page 修改 PageName 为 `FlutterContainerPage`, 点击 Finish,  随后修改页面内容如下：

```ts
import { FlutterEntry, FlutterPage, FlutterView } from '@ohos/flutter_ohos'

@Entry
@Component
struct Index {

  private flutterEntry?: FlutterEntry;
  private flutterView?: FlutterView;

  aboutToAppear() {
    this.flutterEntry = new FlutterEntry(getContext(this));
    this.flutterEntry.aboutToAppear();
    this.flutterView = this.flutterEntry.getFlutterView();
  }

  aboutToDisappear() {
    this.flutterEntry?.aboutToDisappear();
  }

  onPageShow() {
    this.flutterEntry?.onPageShow();
  }

  onPageHide() {
    this.flutterEntry?.onPageHide();
  }

  build() {
    RelativeContainer() {
      FlutterPage({ viewId: this.flutterView?.getId()})
    }
  }
}
```

FlutterPage 是 OpenHarmony Flutter SDK 提供的一个组件，用于在 ArkUI中渲染 Flutter 页面。其原理是使用了 ArkUI 中的 XComponent 来自定义渲染内容。


## 参考资料

- [如何使用混合开发 module](https://gitcode.com/openharmony-sig/flutter_samples/blob/br_3.7.12-ohos-1.1.0/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%B7%B7%E5%90%88%E5%BC%80%E5%8F%91%20module.md)
- [如何使用混合开发添加跳转 FlutterEntry](https://gitcode.com/openharmony-sig/flutter_samples/blob/br_3.7.12-ohos-1.1.0/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%B7%B7%E5%90%88%E5%BC%80%E5%8F%91%E6%B7%BB%E5%8A%A0%E8%B7%B3%E8%BD%AC%20FlutterEntry.md)