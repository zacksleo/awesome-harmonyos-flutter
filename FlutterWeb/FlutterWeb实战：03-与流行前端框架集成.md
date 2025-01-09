# 与流行前端框架集成

> 前端有非常多的框架、工具、库，这些都要比 Dart Web 成熟、丰富。所以在将 Fluttter 编译成 Web 以后，若能使用现有的前端技术实现 web 端的特殊需求，肯定事半功倍。

## 搭建框架

在开始之前，确保你已经安装好了 node 和 npm

### 使用 create-react-app 初始化项目

首先使用 create-react-app 创建一个前端项目

```bash
npx create-react-app flutter_web
```

这些创建以下文件

```bash
  .eslintrc.js
  build/
  node_modules/
  package.json
  public/
  src/
  yarn.lock
```

这是一个标准的前端项目，不过不用担心，我们不会使用任何 react 技术。

## 项目配置

为了能自定义 webpack 打包配置，需要安装一个名为 `react-app-rewired` 的插件，以替换 `react-scripts` 脚本

### 使用 react-app-wired 替换

安装 react-app-wired

```bash
npm install -g react-app-rewired
```

在根目录创建 `config-overrides.js` 文件，增加以下内容

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = function override(config, env) {
  // Remove the default HtmlWebpackPlugin
  config.plugins = config.plugins.filter(
    (plugin) => !(plugin instanceof HtmlWebpackPlugin)
  );

  // Add your own HtmlWebpackPlugin instance with your own options
  config.plugins.push(
    new HtmlWebpackPlugin({
      template: 'public/index.html',
      minify: {
        removeComments: false,
        collapseWhitespace: false,
        removeRedundantAttributes: true,
        useShortDoctype: true,
        removeEmptyAttributes: true,
        removeStyleLinkTypeAttributes: true,
        keepClosingSlash: true,
        minifyJS: false,
        minifyCSS: true,
        minifyURLs: true,
      },
    })
  );

  return config;
};
```

这里面引入一个名为 `html-webpack-plugin` 的插件，配置了需要压缩的内容。


替换 package.json 中 scripts 部分

```diff
  "scripts": {
-   "start": "react-scripts start",
+   "start": "react-app-rewired start",
-   "build": "react-scripts build",
+   "build": "react-app-rewired build",
-   "test": "react-scripts test",
+   "test": "react-app-rewired test",
    "eject": "react-scripts eject"
}
```

## 初始化 Flutter

在当前项目目录下，执行以下命令初始化 Flutter 项目

```bash
flutter create --platforms web .
```

这将创建一个 Flutter 项目，并添加了 web 平台支持。

以下目录由 flutter 创建

```
Recreating project ....
  pubspec.yaml (created)
  lib/main.dart (created)
  web/
  analysis_options.yaml (created)
```

## 集成

现在，虽然两个项目共用一个目录，但我们需要修改一些配置，才将 flutter 项目与前端项目集成在一起工作。


编辑 package.json 文件中`scripts/build` 处的内容，改为

"rm -rf build && rm -rf web && react-app-rewired build && mv build web"，

同时删除不需要的依赖, 增加 `react-app-rewired` 依赖

```diff
  "dependencies": {
-    "cra-template": "1.2.0",
-    "react": "^19.0.0",
-    "react-dom": "^19.0.0",
      "react-scripts": "5.0.1"
  },
  "eslintConfig": {
    "extends": [
-      "react-app",
-      "react-app/jest"
    ]
  },
+ "devDependencies": {
+   "react-app-rewired": "^2.2.1"
+  }
```

运行 `npm install` 或 `yarn install`, 更新依赖。

这行命令的作用是，构建时先清理当前项目目录 build 和 web 目录，构建完成后将前端构建目录改名为 web，以提供给 flutter 进一步构建使用。

最终，经过一番折腾， `package.json` 文件中的内容如下面所示

```json
{
  "name": "flutter_web",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react-scripts": "5.0.1"
  },
  "scripts": {
    "start": "react-app-rewired start",
    "build": "rm -rf build && rm -rf web && react-app-rewired build && mv build web",
    "test": "react-app-rewired test",
    "eject": "react-app-rewired eject"
  },
  "eslintConfig": {
    "extends": [
    ]
  },
    ...
  "devDependencies": {
    "react-app-rewired": "^2.2.1"
  }
}
```

接下来我们复制 web 目录并替换掉 public 目录。

```bash
rm -rf public
cp -r web public
```

运行 `npm run build`, 如果能成功生成 web 目录，代表集成成功

## 前端开发

前面的准备工作完成以后，就可以愉快的开发了！

进入 src 目录，这里面就可以编写我们的前端代码了，也可以使用 npm 的任何 js 库。

为了统一维护 js，我们把 flutter web 的初始化代码从 html 中移到这里。

首先清空 src 目录中的文件，然后新建一个 `index.js`, 添加以下内容

```js
window.flutterWebRenderer = "html";
window.addEventListener('load', function(ev) {
  // Download main.dart.js
  _flutter.loader.loadEntrypoint({
    entrypointUrl: 'main.dart.js',
    serviceWorker: {
      serviceWorkerVersion: serviceWorkerVersion,
    }
  }).then(function(engineInitializer) {
    return engineInitializer.initializeEngine();
  }).then(function(appRunner) {
    return appRunner.runApp();
  });
});
```

## 项目构建

```bash
# 构建前端
npm run build
# 构建Flutter
flutter clean && flutter build web --build-number=$VERSION_CODE --build-name=$TAG --web-renderer html --profile --base-href /webapp/
```

上述命令中， 和  都是环境变量，需要提前设置好

```bash
export VERSION_CODE=1
export TAG=1.0.0
```

这里需要注意的是，如果你不希望通过子目录访问 Flutter web 应用，那么需要将 `base-href` 设置为 `/`，或者移除该选项

```bash
flutter clean && flutter build web --build-number=$VERSION_CODE --build-name=$TAG --web-renderer html --profile
```

## 源码获取

https://gitee.com/zacks/flutter-web-demo

## 参考资料

- [Create React App](https://github.com/facebook/create-react-app)
- [React App Wired](https://github.com/timarney/react-app-rewired/blob/master/README_zh.md)
- [编写你的第一个 Flutter 网页应用](https://docs.flutter.cn/get-started/codelab-web/)
- [flutter-web-demo](https://gitee.com/zacks/flutter-web-demo)
