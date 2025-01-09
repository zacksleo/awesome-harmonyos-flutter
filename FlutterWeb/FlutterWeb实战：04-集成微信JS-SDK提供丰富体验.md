>微信的 JS-SDK 提供了很多调用微信能力的API，H5 页面也经常用到。本文以文件上传为为例，介绍了如何在Flutter Web项目集成微信 JS-SDK。

## 配置 js-sdk

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

这块没有特殊内容，只是把原来调用初始化方法改为了Promise方式。

## 上传图片

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
与前面相似，同样将图片上传封住成立Promise形式，方便后续调用。

## 封装导出

我们把上述代码封住到一个对象中，统一导出调用。

```js
import * as sdk from "./forest/index.js";

window.sdk = sdk;
```

这里可以把 sdk 改为你希望使用的名字。