## 概述

在上一章中，我们介绍了如何初始化 Flutter 引擎，本文重点介绍如何添加并跳转至 Flutter 页面。

## 跳转原理

跳转原理如下：

本质上是从一个原生页面A 跳转至另一个原生页面 B，不过区别在于，页面 B是一个页面容器，内嵌了 Flutter 内容。
同时当打开页面 B 之前，我们需要通知 Flutter 提前切换页面，这里使用了 Flutter 提供的通信机制，也就是 EventChannel。


## 添加 FlutterPage

为了显示 Flutter 内容，我们需要创建一个原生页面，作为承载 Flutter 的容器。

在 `entry/src/main/etc/pages` 目录下添加一个页面, 例如名称为 `FlutterContainerPage`, 鼠标右键点击 `ohos/entry/src/main/ets/pages` 目录，依次选择 New->Page->Empty Page 修改 PageName 为 `FlutterContainerPage`, 点击 Finish,  随后修改页面内容如下：

```ts
import { FlutterEntry, FlutterPage, FlutterView } from '@ohos/flutter_ohos'

@Entry
@Component
struct Index {

  private flutterEntry?: FlutterEntry;
  private flutterView?: FlutterView;

  aboutToAppear() {
    this.flutterEntry = new FlutterEntry(getContext(this));
    this.flutterEntry.aboutToAppear();
    this.flutterView = this.flutterEntry.getFlutterView();
  }

  aboutToDisappear() {
    this.flutterEntry?.aboutToDisappear();
  }

  onPageShow() {
    this.flutterEntry?.onPageShow();
  }

  onPageHide() {
    this.flutterEntry?.onPageHide();
  }

  build() {
    RelativeContainer() {
      FlutterPage({ viewId: this.flutterView?.getId()})
    }
  }
}
```

FlutterPage 是 OpenHarmony Flutter SDK 提供的一个组件，用于在 ArkUI中渲染 Flutter 页面。其原理是使用了 ArkUI 中的 XComponent 来自定义渲染内容。


## 修改原生页面

```ts
import { router } from '@kit.ArkUI';

@Entry
@Component
struct Index {
  build() {
    Column() {
      Text('Hello World').fontSize('50fp').fontWeight(FontWeight.Bold)
      Blank().height(80)
      Button('跳转Flutter').onClick(() => {
        router.pushUrl({ url: 'pages/FlutterContainerPage'})
      })
    }.justifyContent(FlexAlign.Center)
    .alignItems(HorizontalAlign.Center)
    .width('100%')
    .height('100%')
  }
}
```

我们在原生页面处添加一个按钮，点击按钮时跳转至 Flutter 页面。

## 接下来

在上面的例子中，每次打开的 Flutter 页面是固定的，接下来我们将探讨如何实现动态跳转 Flutter 页面。

## 参考资料

- [如何使用混合开发添加跳转 FlutterEntry](https://gitcode.com/openharmony-tpc/flutter_samples/blob/br_3.7.12-ohos-1.1.0/ohos/docs/04_development/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%B7%B7%E5%90%88%E5%BC%80%E5%8F%91%E6%B7%BB%E5%8A%A0%E8%B7%B3%E8%BD%AC%20FlutterEntry.md)
- [flutter_page_sample2](https://gitcode.com/openharmony-tpc/flutter_samples/tree/br_3.7.12-ohos-1.1.0/ohos/flutter_page_sample2)
- [flutter_page_sample1](https://gitcode.com/openharmony-tpc/flutter_samples/tree/br_3.7.12-ohos-1.1.0/ohos/flutter_page_sample1)