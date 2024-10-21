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

3. common 里面是领域对象，小组件，服务类，扩展等，平台无关

4. apps 是应用外套，通过组合不同的模块，向不同的平台打包。

5. 使用lerna管理多包仓库。

其中apps下的项目，则为需要打包成各平台，各app的入口项目。里面主要为项目配置代码，模块依赖配置，以及特定的平台适配代码。

在apps目录下新建鸿蒙项目，先把壳项目在鸿蒙中跑起来，确保没有问题。依次再添加依赖项，首先添加纯dart编写的包，再添加依赖于原生代码/插件的包。注意挨个添加依赖，不要一次添加太多依赖，方便排查定位问题，

解决版本依赖问题，鸿蒙Flutter项目目前需要依赖于3.7版本，如果原项目使用了更低的版本，则可将原项目SDK依赖升级至3.7；如果原项目SDK版本高于3.7，则有两种方案：一种是降级原项目SDK依赖为3.7；另外一种是使用多分支方案。