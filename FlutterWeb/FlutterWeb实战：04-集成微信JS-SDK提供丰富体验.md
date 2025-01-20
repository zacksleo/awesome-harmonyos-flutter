>微信的 JS-SDK 提供了很多调用微信能力的 API，H5 页面也经常用到。本文以文件上传为为例，介绍了如何在 Flutter Web 项目集成微信 JS-SDK。

## 配置 JS-SDK

首先，我们需要对微信 JS-SDK 进行初始化配置。以下代码将原本的初始化方法封装为 Promise 形式，便于后续调用。

```js
/**
 * 配置js
 * @param {*} options
 * @returns
 */
export async function configJsSdk(options) {
  return new Promise((resolve, reject) => {
    wx.config(options);
    wx.ready(function () {
      resolve();
    });
    wx.error(function () {
      reject();
    });
  });
}
```

通过这种方式，我们可以在配置完成后执行后续操作，确保 JS-SDK 的正确初始化。

## 实现图片上传功能

接下来，我们实现图片上传功能。微信 JS-SDK 提供了 chooseImage 和 uploadImage 两个 API，分别用于选择图片和上传图片。我们将这两个 API 封装为 Promise 形式，方便在 Flutter Web 中调用。

```js
/**
 * 上传图片
 * @returns
 */
export async function uploadImage() {
  return upload(['album', 'camera']);
}

/**
 * 拍照上传
 * @returns
 */
export async function uploadCamera() {
  return upload(['camera']);
}

export async function upload(sourceType) {
  return new Promise((resolve, reject) => {
    wx.chooseImage({
      // 默认9
      count: 1,
      // 可以指定是原图还是压缩图，默认二者都有
      sizeType: ['original', 'compressed'],
      // 可以指定来源是相册还是相机，默认二者都有
      sourceType: sourceType,
      // 返回选定照片的本地 ID 列表，localId可以作为 img 标签的 src 属性显示图片
      success: function (res) {
        wx.uploadImage({
          // 需要上传的图片的本地ID，由 chooseImage 接口获得
          localId: res.localIds[0],
          // 默认为1，显示进度提示
          isShowProgressTips: 1,
          // 返回图片的服务器端ID
          success: function (res) {
            resolve(res.serverId);
          },
          fail: function (res) {
            reject(res);
          }
        })
      }
    });
  })
}
```

通过封装，我们可以轻松实现从相册或相机选择图片并上传的功能。


## 服务端处理文件上传

在客户端上传图片后，服务端需要接收微信返回的 mediaId，并调用微信 API 下载文件。以下是服务端的实现步骤。

### 引入依赖

首先，在 pom.xml 中引入 wx-java-mp 依赖包：

```xml
    <!-- 微信公众号 -->
    <dependency>
      <groupId>com.github.binarywang</groupId>
      <artifactId>wx-java-mp-spring-boot-starter</artifactId>
      <version>4.4.0</version>
    </dependency>
```

### 下载文件

在服务端，我们可以通过 WxMpService 提供的 API 下载文件：

```java

  @Autowired
  private WxMpService wxMpService;

  File file = wxMpService.getMaterialService().mediaDownload(encode(request.getMediaId()));
```

通过 mediaDownload 方法，我们可以根据 mediaId 下载文件，并进行后续处理。

## 封装与导出

为了方便在 Flutter Web 项目中调用，我们将上述功能封装为一个对象，并导出到全局作用域：

```js
import * as flutterWeb from "./app/index.js";

window.flutterWeb = flutterWeb;
```

这里可以把 flutterWeb 改为你希望使用的名字。

在 Web 中，可以通过 flutterWeb 对象调用相关方法，我们将在后续文章介绍，如何在 Flutter Web 中调用 Web 中的 API。

## 总结

本文详细介绍了如何在 Flutter Web 项目中集成微信 JS-SDK，并实现图片上传功能。通过封装 JS-SDK 的 API 和服务端处理逻辑，我们可以轻松实现与微信的深度集成，为用户提供更丰富的功能体验。希望本文能为开发者提供有价值的参考。

## 参考资料

- [JS-SDK说明文档](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html)
- [微信开发 Java SDK](https://github.com/binarywang/WxJava/tree/develop)