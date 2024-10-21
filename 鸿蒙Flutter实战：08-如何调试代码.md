# 鸿蒙Flutter实战：如何调试代码

## 1.环境搭建

参考文章[鸿蒙Flutter实战：01-搭建开发环境](https://gitee.com/zacks/awesome-harmonyos-flutter/blob/master/%E9%B8%BF%E8%92%99Flutter%E5%AE%9E%E6%88%98%EF%BC%9A01-%E6%90%AD%E5%BB%BA%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83.md)搭建好开发环境。IDE 安装好 DevEco 和 VsCode/Android Studio。

## 2.配置



## 3.查看日志

## 4.调试 Flutter

主要有两种调试方案。

### 方案一

在IDE 中直接运行 Flutter 项目，IDE 可选择 Andriod Studio 或者 VsCode，在调试栏点击 Debug 运行。

### 方案二

适应DecEco运行鸿蒙项目，注意需要打开的是ohos鸿蒙目录代码，待IDE分析结束后，点击运行。

当app在鸿蒙设备上启动成功后，立即在 Vscode 中调出 Command Pallet，找到 Flutter Attach ，将 Flutter 调试器连接至宿主机

然后就是增加断点，使用hot reload 重新加载 Flutter，调试项目代码。

## 调试 ArkTs

需要使用 DevEcho 打开项目，点击运行旁边的 Debug Entry 按钮，开始程序调试。

## 调试 Webview

参考文章 [鸿蒙Flutter实战：04-如何使用DevTools调试Webview](https://gitee.com/zacks/awesome-harmonyos-flutter/blob/master/%E9%B8%BF%E8%92%99Flutter%E5%AE%9E%E6%88%98%EF%BC%9A04-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8DevTools%E8%B0%83%E8%AF%95Webview.md)进行 Webview 调试。
