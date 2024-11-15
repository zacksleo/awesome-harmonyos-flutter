# 使用 Flutter SDK 3.22.0

## SDK 安装

参考[鸿蒙Flutter实战：01-搭建开发环境]文章的说明，首先安装 Flutter SDK 3.22.0。

目前鸿蒙化Flutter SDK 3.22 还未正式发布，现在可以使用 `https://gitee.com/harmonycommando_flutter/flutter` 进行前期测试验证。

使用 FVM 进入 目录 `~/fvm/versions/`, 克隆以上仓库。

```bash
git clone https://gitee.com/harmonycommando_flutter/flutter.git custom_3.22.0
```

接下来使用 `fvm list` 命令查看 SDK版本 列表。

```bash
┌───────────────┬─────────┬─────────────────┬──────────────┬──────────────┬────────┬───────┐
│ Version       │ Channel │ Flutter Version │ Dart Version │ Release Date │ Global │ Local │
├───────────────┼─────────┼─────────────────┼──────────────┼──────────────┼────────┼───────┤
│ custom_3.22.0 │         │ Need setup      │              │              │        │       │
├───────────────┼─────────┼─────────────────┼──────────────┼──────────────┼────────┼───────┤
│ 3.22.0        │ stable  │ 3.22.0          │ 3.4.0        │ May 13, 2024 │ ●      │       │
└───────────────┴─────────┴─────────────────┴──────────────┴──────────────┴────────┴───────┘
```

可以看到，SDK中出现了两个版本，其中使用命令 `fvm global 3.22.0` 将 官方的3.22.0 设置成了全局默认版本。鸿蒙化的 SDK 需要配置安装，我们稍后进入项目，执行安装。

## 项目配置

1.进入项目根目录，如果项目还未创建，则使用 `flutter create` 命令创建项目

```bash
flutter create my_app
```

2.在当前项目目录，设置使用的 Flutter SDK 版本

```bash
fvm use custom_3.22.0
```

此时会自动安装 sdk 版本，运行成功后如果再运行 `fvm list`, 可以看到 SDK 已经准备就绪。

```text
┌───────────────┬─────────┬─────────────────┬──────────────┬──────────────┬────────┬───────┐
│ Version       │ Channel │ Flutter Version │ Dart Version │ Release Date │ Global │ Local │
├───────────────┼─────────┼─────────────────┼──────────────┼──────────────┼────────┼───────┤
│ custom_3.22.0 │         │ 3.22.0-ohos     │ 3.4.0        │              │        │       │
├───────────────┼─────────┼─────────────────┼──────────────┼──────────────┼────────┼───────┤
```

同时，配置命令执行完成后，将会在项目目录中创建 `.fvm` 目录，里面 flutter_sdk 会软连接到实际的 custom_3.22.0 SDK 目录。

查看 .vscode/settings.json 文件可以发现，自动创建了一条配置 flutter sdk 的项目：

```json
  "dart.flutterSdkPath": ".fvm/versions/custom_3.22.0"
```

如果项目使用了 melos, 则需要在 `melos.yaml` 文件的底部，添加以下配置，使得 melos 可以使用自定义的 flutter sdk

```yaml
sdkPath: .fvm/versions/custom_3.22.0
```

3.如果项目已经创建，还未添加鸿蒙平台支持，则使用以下命令添加鸿蒙平台支持。

```bash
flutter create --platforms ohos .
```

其中，`.`代表当前目录。

目录结构类似如下所示

```bash
├── README.md
├── analysis_options.yaml
├── assets
├── build
├── env
├── lib
│   ├── config
│   └── main.dart
├── melos_ohos_app.iml
├── ohos
│   ├── AppScope
│   ├── build-profile.json5
│   ├── entry
│   ├── har
│   ├── hvigor
│   ├── hvigorfile.ts
│   ├── local.properties
│   ├── oh-package-lock.json5
│   ├── oh-package.json5
│   └── oh_modules
├── pubspec.lock
├── pubspec.yaml
└── pubspec_overrides.yaml
```

创建命令执行成功后，项目中会出现 `ohos`目录，这里面存放的就是鸿蒙平台的相关代码。

## 签名

1.在运行项目前，先对项目进行签名，否则在运行过程中会出现这样的错误

```text
请通过DevEco Studio打开ohos工程后配置调试签名(File -> Project Structure -> Signing Configs 勾选Automatically generate signature)
```

2.用 DevEco 打开上面的 `ohos` 目录，注意不是项目目录，是项目下面的 ohos 鸿蒙目录，然后根据提示依次打开 `File -> Project Structure -> Signing Configs`, 点击自动签名即可。

3.签名成功后，文件 `ohos/build-profile.json5` 会自动更新，里面的字段 `signingConfigs` 出现相应的签名配置信息。

## 运行

 运行 Flutter 项目，在项目根目录使用 `fvm flutter run` 或者在 IDE 中点击运行按钮

## 参考资料

- [FVM](https://fvm.app/)
- [鸿蒙Flutter实战：01-搭建开发环境](./鸿蒙Flutter实战：01-搭建开发环境.md)
- [鸿蒙 Flutter 3.22.0](https://gitee.com/harmonycommando_flutter/flutter)
