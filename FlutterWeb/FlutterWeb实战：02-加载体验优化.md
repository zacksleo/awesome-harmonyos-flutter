## 背景

默认情况下，flutter打包web以后，首次打开页面需要加载大量的页面，这就需要做首屏加载优化

## 渲染引擎

通过分析，wasmgc和skia需要加载较大的引擎包，很难优化，故选择html引擎

## 字体图标裁剪

体积裁剪，通过 bulid apk shaking icon，得到一个裁剪后的字体库，替换调flutter web打包的对应字体产物

先在app项目构建 apk： flutter build apk --tree-shake-icons

找到 `build/host/intermediates/assets/release/mergeReleaseAssets/flutter_assets/fonts/MaterialIcons-Regular.otf`
将该文档复制到 `web/fonts/` 文件夹

## 延迟加载

使用延迟加载拆分文件，当前页面不需要的使用的代码延迟加载

## 加载动画

增加过渡动画，在资源加载过程中使用一个加载动画，优化用户体验。

这里使用 flutter_native_splash 插件，在 app 启动时，显示一个加载动画，在 app 加载完成后，隐藏加载动画。

```html
<body>
  <picture id="splash">
    <img class="center" width="95" height="100" aria-hidden="true" src="loading.gif" alt="">
  </picture>
  <script type="text/javascript" src="splash/splash.js"></script>
</body>
```

增加以下 css 样式

```css
html {
  height: 100%
}

body {
  margin: 0;
  min-height: 100%;
  background-size: 100% 100%;
  -webkit-text-size-adjust: 100% !important;
  text-size-adjust: 100% !important;
  -moz-text-size-adjust: 100% !important;
}

.center {
  margin: 0;
  position: absolute;
  top: 50%;
  left: 50%;
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}
```

splash/splash.js 的内容如下：

```js
function removeSplashFromWeb() {
  document.getElementById("splash")?.remove();
  document.getElementById("splash-branding")?.remove();
  document.body.style.background = "transparent";
}
```


在 Flutter main.dart 中，配置加载动画保持, 我们将在后面手动移除。

```dart
void main() {
    FlutterNativeSplash.preserve(widgetsBinding: widgetsBinding);
}
```

在 AppDefere 中，移除加载动画

```dart
FlutterNativeSplash.remove();
```

![](./.figures/loading.gif)


## GZIP压缩

开启gzip，压缩静态资源文件。

```lua
    gzip  on;
    gzip_min_length 1k;
    gzip_comp_level 5;
    gzip_vary on;
    gzip_static on;
    gzip_types text/plain text/html text/css application/javascript application/x-javascript text/xml application/xml application/xml application/json;
```

这里配置了压缩文件类型，如 text/plain, html，css, javascript json 等。

Gzip 压缩开启之后，可以在浏览器的开发者工具中，打开网络面板，查看响应头中，有一个 Content-Encoding: gzip 的字段，表示该文件已经被压缩。

经过下表中的采样对比可以看到，压缩率还是很高的。

|文件采样| 压缩前 | 压缩后 | 压缩率 |
|--- | --- | --- | --- |
|main.dart.js| 3.1M | 903k | 28% |
|vendor.js| 2.6M | 667k | 25% |
|app.js| 1M | 185k | 18% |


## CDN

也可以将静态资源放到 CDN 上，如阿里云等，通过 OSS 存储，然后配置 CDN 加速。需要注意的事，这要做好版本控制，否则会出现缓存问题。

## 参考资料

- [Flutter Web加载优化](https://segmentfault.com/a/1190000042664763)
- [How to Optimize Flutter Web and How Flutter Web work in Html Renderer](https://medium.com/@GSYTech/how-to-optimize-flutter-web-and-how-flutter-web-work-in-html-renderer-b399ffd66718)
- [flutter_native_splash](https://pub.dev/packages/flutter_native_splash)
- [延迟加载组件](https://docs.flutter.cn/perf/deferred-components/)
- [gzip压缩检测](https://www.wetools.com/gzip)

