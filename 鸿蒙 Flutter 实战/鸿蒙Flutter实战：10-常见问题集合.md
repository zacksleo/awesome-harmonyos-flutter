# 鸿蒙Flutter实战：10-常见问题集合

## 1. 学习路径应该是怎样的，需要掌握哪些技术才具备鸿蒙 Flutter 开发能力

1.1 学习和掌握 Flutter 开发技术，这块需要在Flutter社区学历 [Flutter开发文档](https://docs.flutter.cn/)
1.2 学习鸿蒙基础概念和知识，推荐学习 [鸿蒙生态应用开发白皮书](https://developer.huawei.com/consumer/cn/doc/guidebook/harmonyecoapp-guidebook-0000001761818040), [ArkTS 语言](https://developer.huawei.com/consumer/cn/arkts/), [ArkUI](https://developer.huawei.com/consumer/cn/arkui/),
[HarmonyOS 第一课](https://developer.huawei.com/consumer/cn/teaching-video/)

## 2. MatePad 应用适配问题

如果出现 app 在 Matepad 上无法全屏的问题，需要在 ohos/entry/main/module.json5中配置设备类型：

```json
    "deviceTypes": [
      "phone",
      "tablet",
      "car",
      "2in1",
      'default'
    ],
```

需要增加 `tablet` 平板设备的适配。

如果在 Matepad 上运行时设备没有全屏，则可以需要删除 App 重装安装或者重启设备。因为相关的配置存在缓存，适配类型发生变化时，存在没有更新的问题，导致无法全屏。

## 3. 模拟器

模拟器与真机有较大差异，如果出现模拟器异常情况，优先确实真机是否正常运行，以排除模拟器自身问题。

## 4. debug 版本运行报错

`Error while initializing the Dart VM`

```text
依次执行以下操作
设置环境变量 export FLUTTER_STORAGE_BASE_URL=https://flutter-ohos.obs.cn-south-1.myhuaweicloud.com
删除 /bin/cache 目录下的缓存
执行 flutter clean，清除项目编译缓存
运行 flutter run -d $DEVICE --debug
```

## 5. 如何更换 App 图标和名称

找到 ohos/AppScope/resources/base/media/app_icon.png，替换相应的文件

找到 ohos/AppScope/resources/base/element/string.json 文件，修改以下配置

```json
{
  "string": [
    {
      "name": "app_name",
      "value": "中文名称"
    }
  ]
}
```

## 6. flutter run 运行 App 报错，提示命令找不到

```bash
Launching lib/main.dart on 127.0.0.1:5555
start hap build..-e ERROR: node: /Applications/DevEco-Studio.app/Contents/tools/ohpm/bin/ohpm: line 7: node: commandnot found
-e ERROR: NODE_HOME: /Applications/DevEco-Studio.app/Contents/tools/ohpm/bin/ohpm: line 11: /node:
o such file or directory
-e ERROR: NODE_HOME: /Applications/DevEco-Studio.app/Contents/tools/ohpm/bin/ohpm: line 25: /bin/noc
e: No such file or directory
-e ERROR: Failed to find the executable 'node’ command, please check the following possible causes:e1. Node]s is not installed.e2.'node'command not added to PATH;
eand the 'NoDE HOME' variable is not set in the environment variables to match your NodeJsinstallation location.
ProcessException: The command failedCommand: ohpm clean
```

检查环境变量配置，配置成功后，检查是否已生效。通过 `source ~/.zshrc` 或重启命令行程序，甚至重启 IDE/系统，直至变量生效。

## 7.是否可以使用 Flutter 开发元服务

目前不行，元服务大小有限制 （2M），Flutter 构建产物过大，不符合这一要求

### 8. 如何自定义显示 DevEco 打开 ohos 后的项目名称

每个鸿蒙Flutter项目，用DevEco打开ohos工程后，默认显示的工程名称为 `ohos`，如果想自定义显示的工程名称，可以参考以下步骤：

在 ohos/.idea 目录下，新建一个 `.name` 文件，写入项目名称即可。

## 参考资料

- [Flutter SDK 仓库-常见问题](https://gitcode.com/openharmony-sig/flutter_flutter#%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98)