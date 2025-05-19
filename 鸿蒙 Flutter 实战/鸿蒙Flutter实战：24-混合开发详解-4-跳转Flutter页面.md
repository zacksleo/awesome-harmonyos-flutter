## 配置

将 Flutter 模块添加至宿主鸿蒙项目中后，接下需要实现页面跳转、信息通信等功能，本文重点介绍如何跳转至 Flutter 页面。


修改 Ohos 的入口文件, 将 Flutter 模块生成的 .ohos目录中的 EntryAbility.ets 和 Index.ets 文件复制到宿主工程中进行替换

```
cp flutter_module/.ohos/entry/src/main/ets/entryability/EntryAbility.ets ohos_app/entry/src/main/ets/entryability/EntryAbility.ets
cp flutter_module/.ohos/entry/src/main/ets/pages/Index.ets ohos_app/entry/src/main/ets/pages/Index.ets
```

其中 `EntryAbility` 继承自 `FlutterAbility`，而 `FlutterAbility` 继承自 `UIAbility`, 它在 `UIAbility` 上增加了以下功能：


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