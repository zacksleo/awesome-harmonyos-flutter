# 鸿蒙Flutter实战：02-Windows环境搭建踩坑指南

## 环境搭建

### 1. 下载Flutter SDK，配置环境变量

 鸿蒙 Flutter SDK 需要在 [Gitee 下载](https://gitcode.com/openharmony-sig/flutter_flutter)。目前建议下载 dev 分支代码。

#### 需要配置以下用户变量

注意鸿蒙开发需要安装Java和配置相关变量

```bash
# flutter sdk 镜像
FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
# pub 镜像
PUB_HOSTED_URL=https://pub.flutter-io.cn

DEVECO_SDK_HOME=C:\Program Files\Huawei\DevEco Studio\sdk
# Java SDK
JAVA_HOME=C:\Program Files\Huawei\DevEco Studio\jbr
```

#### 配置环境变量

编辑 PATH，添加以下路径，鸿蒙开发需要配置ohpm, hvigor及node

```bash
C:\Program Files\Huawei\DevEco Studio\tools\ohpm\bin

C:\Program Files\Huawei\DevEco Studio\tools\hvigor\bin

C:\Program Files\Huawei\DevEco Studio\tools\node

C:\Program Files\Huawei\DevEco Studio\jbr\bin
```

SDK 下载完成，环境变量配置妥当后，使用 flutter doctor 检查各项是否通过。

在命令行中，运行 ohpm -v, hvigorw -v, node -v 查看是否能使用，确保各个依赖的工具，其 PATH 配置正确。

使用 `echo %DEVECO_SDK_HOME%`, `echo %JAVA_HOME%` 等检查用户变量是否生效。

环境变量发生变化时，需要重启命令行工具。

另外，需要注意的是，优先添加用户环境变量，如果是系统环境变量，可能需要注销登录或者重启系统，否则配置可能不生效。

### 2. 为了避免意外情况，将新建项目位置，与SDK使用相同的磁盘，如D盘。

否则可能出现 package 找不到的情况。

另外，项目目录不要过深，不然会因路径太长导致编译可能失败。

### 3. VsCode 无法识别设备

用 DevEco 打开项目，待项目分析完成后，Vscode 中的设备应该可以出来了。

### 4. 插件Har包找不到

打开DevEco运行时，出现类似以下错误

```bash
hpm ERROR: missing: flutter_inappwebview_ohos@/.../ohos/har/flutter_inappwebview_ohos.har, required by entry@1.0.0
ohpm ERROR: Found exception: Error: Fetch local file package error, /.../ohos/har/flutter_inappwebview_ohos.har does not exist., reached retry limit or non retryable error encountered.
ohpm ERROR: missing: video_player_ohos@/.../ohos/har/video_player_ohos.har, required by entry@1.0.0
ohpm ERROR: Found exception: Error: Fetch local file package error, /.../ohos/har/video_player_ohos.har does not exist., reached retry limit or non retryable error encountered.
ohpm ERROR: missing: path_provider_ohos@/.../ohos/har/path_provider_ohos.har, required by entry@1.0.0
ohpm ERROR: Found exception: Error: Fetch local file package error, /.../ohos/har/path_provider_ohos.har does not exist., reached retry limit or non retryable error encountered.
ohpm ERROR: missing: shared_preferences_ohos@/.../ohos/har/shared_preferences_ohos.har, required by entry@1.0.0
ohpm ERROR: Found exception: Error: Fetch local file package error, /.../ohos/har/shared_preferences_ohos.har does not exist., reached retry limit or non retryable error encountered.
ohpm ERROR: missing: image_picker_ohos@/.../ohos/har/image_picker_ohos.har, required by entry@1.0.0
ohpm ERROR: Found exception: Error: Fetch local file package error, /.../ohos/har/image_picker_ohos.har does not exist., reached retry limit or non retryable error encountered.
ohpm ERROR: missing: @ohos/flutter_ohos@/.../ohos/har/flutter.har, required by @
ohpm ERROR: Found exception: Error: Fetch local file package error, /.../ohos/har/flutter.har does not exist., reached retry limit or non retryable error encountered.
ohpm ERROR: Install failed, detail: Error: Fetch local file package error, /.../ohos/har/flutter_inappwebview_ohos.har does not exist.
```

此时需要在Flutter项目下运行 `flutter run` 或 `flutter build` 以生成插件的 har 包

### 4. 如何自定义显示 DevEco 打开 ohos 后的项目名称

每个鸿蒙Flutter项目，用DevEco打开ohos工程后，默认显示的工程名称为 `ohos`，如果想自定义显示的工程名称，可以参考以下步骤：

在 ohos/.idea 目录下，新建一个 `.name` 文件，写入项目名称即可。

### 5.Harmonvos Sdk not found

```
Harmonvos Sdk not found:please do that, first: download from https://developer .harmonyos.com/cn/develop/deveco-studio#download_clisecond: follow this document:https://developer .harmonyos, com/cn/docs/documentation/doc-guides-V3/ide-command-line-ohsdkmgr-0000001545647965-V3to install OpenHarmony sdk with ohsdkmgr;If the Ohos SDk has been installed to a custom location, please use'flutter config -ohos sdk'to update to that location.
```

解决方法：

根据提示配置 Openharmony sdk 的位置，这个可以打开 Deveco Studio 的设置Settings，在 OpenHarmony Sdk 的 Location 处找到。

```bash
flutter config --ohos-sdk=C:\Users\zacksleo\AppData\Local\OpenHarmony\Sdk
```
