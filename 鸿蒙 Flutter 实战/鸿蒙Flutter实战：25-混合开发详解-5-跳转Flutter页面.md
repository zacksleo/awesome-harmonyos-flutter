## 概述

在上一章中，我们介绍了如何初始化 Flutter 引擎，以及添加一下用于显示 Flutter 内容的原生页面，本文重点介绍如何跳转至 Flutter 页面。

## 跳转原理

跳转原理如下：

本质上是从一个原生页面A 跳转至另一个原生页面 B，不过区别在于，页面 B是一个页面容器，内嵌了 Flutter 内容。
同时当打开页面 B 之前，我们需要通知 Flutter 提前切换页面，这里使用了 Flutter 提供的通信机制，也就是 EventChannel。


原生页面

```ts
import { router } from '@kit.ArkUI';

@Entry
@Component
struct Index {
  @State message: string = 'Hello World';

  build() {
    Column() {
      Text(this.message)
        .id('HelloWorld')
        .fontSize($r('app.float.page_text_font_size'))
        .fontWeight(FontWeight.Bold)
        .onClick(() => {
          this.message = 'Welcome';
        })

      Blank().height(80)

      Button('跳转Flutter').onClick(() => {
        PlatformCall.goToFlutterPage('order/index')
      })
    }.justifyContent(FlexAlign.Center)
    .alignItems(HorizontalAlign.Center)
    .width('100%')
    .height('100%')
  }
}
```