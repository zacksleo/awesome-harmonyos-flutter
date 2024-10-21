# 鸿蒙Flutter实战：10-常见问题集合

1. MatePad 应用适配问题

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


