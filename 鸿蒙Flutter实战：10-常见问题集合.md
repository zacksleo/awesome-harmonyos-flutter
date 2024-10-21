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
