## 准备工作

在前面的文章《FlutterWeb实战：04-集成微信JS-SDK提供丰富体验》中，我们介绍了如何集成微信 JS-SDK，实现与微信 H5 交互。

## 调用小程序API

如果 H5 在微信小程序中打开，还可以调用 JSSDK 提供的小程序相关的 API。以下是可调用的API

```js
wx.miniProgram.navigateTo
wx.miniProgram.navigateBack
wx.miniProgram.switchTab
wx.miniProgram.reLaunch
wx.miniProgram.redirectTo
#向小程序发送消息
wx.miniProgram.postMessag
#获取当前环境
wx.miniProgram.getEnv
```

## 统一登录

一种常用的场景是将部分页面以 H5 形式内嵌到小程序的 Webview 提供次级页面服务。这里面涉及到账号打通的问题。

我们希望当用户在小程序中打开 Webview 页面，不需要登录、授权，就可以直接在 H5 中继续相应的操作。这里有一种方式，可以通过 设置 Cookie 来共享登录状态。

### 统一跳转接口

服务端提供一个API接口，或者称为一个URL地址，形如

`https://xxx.com/app/redirect?accessToken={accessToken}&to={to}`

这个接口接收两个参数，`accessToken`代表用户的 Token，`to` 表示要跳转的页面地址(为确保正确解析，使用urlencode编码)。


假设我们使用 flutter 编写了一个订单页面，其路由为 `/order/index`，那么这个页面的 URL 为 `https://xxx.com/webapp/#/order/index`, 这里面我们使用二级目录托管 Flutter Web 页面，让他与 API 使用相同域名。

当用户在小程序中打开 Webivew 的页面，我们希望用户打开 `https://xxx.com/webapp/#/order/index` 页面，但为了保持登录状态，我们不直接打开这个页面，而是需要通过统一跳转接口中转，

也就是用户打开的是 `https://xxx.com/app/redirect?accessToken={accessToken}&to=/webapp/#/order/index`，在这个接口中，服务端接收两个参数，并向客户端设置 Cookie，同时向客户端发起一个301临时重定向，
小程序的Webview在收到响应后，要自动进行跳转，最终也就跳转到了我们的目的页面，同时本地Cookie中保存的AccessToken，这样也就实现了登录状态共享。

服务端的代码类似如下实现：

```java
// 定义跳转接口
@GetMapping("/app/redirect")
public ResponseEntity<Void> redirectToTargetPage(
    @RequestParam("accessToken") String accessToken,
    @RequestParam("to") String targetUrl,
    HttpServletResponse response) throws IOException {

    // 创建 Cookie 并设置值
    Cookie accessTokenCookie = new Cookie("accessToken", accessToken);
    // 设置 Cookie 的路径，保证整个站点有效
    accessTokenCookie.setPath("/");
    // 设置 Cookie 的过期时间，单位是秒，这里设置为 1小时
    accessTokenCookie.setMaxAge(3600);

    // 将 Cookie 添加到响应中
    response.addCookie(accessTokenCookie);

    // 设置 301 临时重定向
    HttpHeaders headers = new HttpHeaders();
    headers.add("Location", targetUrl);

    // 返回 301 状态码和 Location 头部，触发客户端重定向
    return new ResponseEntity<>(headers, HttpStatus.MOVED_PERMANENTLY);
}
```

> 这里需要注意的是，接口域名必须和跳转页面的域名一致，否则无法共享 Cookie。


## 参考资料

- [JS-SDK说明文档](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html)
- [web-view](https://developers.weixin.qq.com/miniprogram/dev/component/web-view.html)