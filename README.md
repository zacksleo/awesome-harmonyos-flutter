# flutter 鸿蒙适配指南

## 准备工作

1.安装 [DevEco Studio NEXT IDE](https://developer.huawei.com/consumer/cn/deveco-studio/), 注意版本应该是 Next，当前最新的是 Beta3

2.安装Git, 如果要同时适配安卓,需要安装Android Studio; 如果要适配ios,需要安装Xcode


## Mac 安装(推荐)

环境变量配置

```
# Flutter Mirror
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

# HarmonyOS SDK
export TOOL_HOME=/Applications/DevEco-Studio.app/Contents/
export DEVECO_SDK_HOME=$TOOL_HOME/sdk # command-line-tools/sdk
export PATH=$TOOL_HOME/tools/ohpm/bin:$PATH # command-line-tools/ohpm/bin
export PATH=$TOOL_HOME/tools/hvigor/bin:$PATH # command-line-tools/hvigor/bin
export PATH=$TOOL_HOME/tools/node/bin:$PATH # command-line-tools/tool/node/bin
```

## Windows 安装

### 配置用户变量
```
FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

PUB_HOSTED_URL=https://pub.flutter-io.cn

DEVECO_SDK_HOME=C:\Program Files\Huawei\DevEco Studio\sdk

JAVA_HOME=C:\Program Files\Huawei\DevEco Studio\jbr
```

### 配置环境变量

编辑 PATH，添加以下路径
```
C:\Program Files\Huawei\DevEco Studio\tools\ohpm\bin

C:\Program Files\Huawei\DevEco Studio\tools\hvigor\bin

C:\Program Files\Huawei\DevEco Studio\tools\node
```
## 管理多个 Flutter 版本

如果在项目开发中，需要使用多个 Flutter 版本，可以考虑使用 fvm

1. 安装 [FVM](https://fvm.app/)
2. 使用 fvm 官方 flutter 版本

```
fvm install 3.22.0
```
3. 安装自定义鸿蒙版本，进入 fvm/version 目录，通常位于用户目录下，如 `~/fvm/versions/3.22.0`,
拷贝仓库并重命名为 `custom_x.y.z`的名字

```
git clone -b dev https://gitee.com/openharmony-sig/flutter_flutter.git custom_3.7.12
```

4. 在项目中使用单独的 flutter sdk 版本, 在项目目录中执行：

```
fvm use custom_3.7.12
```


## 常见问题

1. 运行 flutter doctor 出现 `Error: Unable to find git in your PATH.`

执行以下命令


```
git config --global --add safe.directory '*'
```

## 参考资料

- [Flutter中文文档](https://docs.flutter.cn/)
- [Harmonyos Next 开发文档](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides-V5/application-dev-guide-V5)
