# 鸿蒙Flutter实战：现有Flutter项目支持鸿蒙

## 背景

原来使用Flutter开发的项目，需要适配鸿蒙。

## 环境搭建

见文章［鸿蒙Flutter适配指南］，搭建开发环境，使用fvm管理多版本SDK。

## 模块化

原有项目保持模块化，拆分为 apps/common/components/modules/plugins等目录，如下所示：

```bash
.
├── README.md
├── analysis_options.yaml
├── melos.yaml
├── melos_ogw-flutter.iml
├── node_modules
├── packages
│   ├── README.md
│   ├── apps
│   │   ├── app
│   │   ├── dsm_app
│   │   ├── ohos_app
│   │   └── web
│   ├── common
│   │   ├── domains
│   │   ├── extensions
│   │   ├── services
│   │   └── widgets
│   ├── components
│   │   ├── image_uploader
│   │   ├── player
│   │   └── scroll_banner
│   ├── modules
│   │   ├── address
│   │   ├── community
│   │   ├── home
│   │   ├── invoice
│   │   ├── me
│   │   ├── message
│   │   ├── order
│   │   ├── shop
│   │   ├── support
│   │   ├── updater
│   └── plugins
│       ├── image_picker
│       ├── printer
├── pubspec.lock
├── pubspec.yaml
└── yarn.lock
```

1. plugins 是依赖于原生平台的插件，

2. components 是平台无关的组件，

3. common 里面是领域对象，小组件，服务类，扩展等，平台无关，里面均为纯 Dart 代码。

4. apps 是应用外套，通过组合不同的模块，向不同的平台打包。

5. 使用 melos 管理多包仓库。

其中apps下的项目，则为需要打包成各平台，各app的入口项目。里面主要为项目配置代码，模块依赖配置，以及特定的平台适配代码。

在apps目录下新建鸿蒙项目，先把壳项目在鸿蒙中跑起来，确保没有问题。依次再添加依赖项，首先添加纯dart编写的包，再添加依赖于原生代码/插件的包。注意挨个添加依赖，不要一次添加太多依赖，方便排查定位问题，

解决版本依赖问题，鸿蒙Flutter项目目前需要依赖于3.7版本，如果原项目使用了更低的版本，则可将原项目SDK依赖升级至3.7；如果原项目SDK版本高于3.7，则有两种方案：一种是降级原项目SDK依赖为3.7；另外一种是使用多分支方案。

## 特定平台工程

在 apps 目录下新建一个项目，该项目运行鸿蒙平台适配和打包。

```bash
flutter create --platforms ohos ohos_app
```

目录结构如下所示：

```bash
.
├── README.md
├── analysis_options.yaml
├── assets
│   ├── icons
│   │   ├── 2.0x
│   │   ├── 3.0x
│   │   └── placeholder.png
│   └── images
│       ├── 2.0x
│       └── 3.0x
├── build
│   ├── ...
├── env
├── lib
│   ├── config
│   │   ├── easy_refresh.dart
│   │   ├── routes.dart
│   │   └── theme.dart
│   └── main.dart
├── ohos
│   ├── AppScope
│   │   ├── app.json5
│   │   └── resources
│   ├── build-profile.json5
│   ├── entry
│   │   ├── build
│   │   ├── build-profile.json5
│   │   ├── hvigorfile.ts
│   │   ├── oh-package-lock.json5
│   │   ├── oh-package.json5
│   │   ├── oh_modules
│   │   └── src
│   ├── har
│   │   ├── ...
│   ├── hvigor
│   │   └── hvigor-config.json5
│   ├── hvigorfile.ts
│   ├── local.properties
│   ├── oh-package-lock.json5
│   ├── oh-package.json5
│   └── oh_modules
│       └── ...
├── pubspec.lock
└── pubspec.yaml
```

可以看到，该项目只是一个壳工程，没有太多代码，主要为项目的一些特定配置，如主题、路由等，以及App入口初始化配置。

编辑 pubspec.yaml 文件，添加组件和模块依赖。

```yaml
environment:
  sdk: '>=2.19.6 <3.0.0'
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
  # 下拉刷新
  easy_refresh: ^3.0.4+2
  flutter_dotenv: ^5.1.0
  go_router: ^6.0.0

  # 领域对象
  domains:
    path: '../../common/domains'
  services:
    path: '../../common/services'
  widgets:
    path: '../../common/widgets'
  address:
    path: '../../modules/address'
  support:
    path: '../../modules/support'
  me:
    path: '../../modules/me'
  message:
    path: '../../modules/message'
  order:
    path: '../../modules/order'
  shop:
    path: '../../modules/shop'
  home:
    path: '../../modules/home'
```

### 配置对特定插件进行鸿蒙化适配

部分第三方插件以及插件依赖的其他库，如果没有适配鸿蒙，则可以通过 override配置鸿蒙化的版本

```yaml
dependency_overrides:
  # ohos
  path_provider:
    git:
      url: "https://gitee.com/openharmony-sig/flutter_packages.git"
      path: "packages/path_provider/path_provider"
```

## 编译运行

运行 Flutter 项目，查看相关日志和运行界面，针对出现的问题再单独处理。
