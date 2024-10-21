# 鸿蒙Flutter实战：02-Windows环境搭建踩坑指南

## 环境搭建

### 1. 下载Flutter SDK，配置环境变量

 鸿蒙 Flutter SDK 需要在 [Gitee 下载](https://gitee.com/openharmony-sig/flutter_flutter)。目前建议下载 dev 分支代码。

#### 需要配置以下用户变量

注意鸿蒙开发需要安装Java和配置相关变量

```bash
# flutter sdk 镜像
FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
# pub 镜像
PUB_HOSTED_URL=https://pub.flutter-io.cn

DEVECO_SDK_HOME=C:\Program Files\Huawei\DevEco Studio\sdk
# Java SDK
JAVA_HOME=C:\Program Files\Huawei\DevEco Studio\jbr
```

#### 配置环境变量

编辑 PATH，添加以下路径，鸿蒙开发需要配置ohpm, hvigor及node

```bash
C:\Program Files\Huawei\DevEco Studio\tools\ohpm\bin

C:\Program Files\Huawei\DevEco Studio\tools\hvigor\bin

C:\Program Files\Huawei\DevEco Studio\tools\node
```

SDK 下载完成，环境变量配置妥当后，使用 flutter doctor 检查各项是否通过。

### 2. 为了避免意外情况，将新建项目位置，于SDK使用相同的磁盘，如D盘。

否则可能出现package找不到的情况。

另外，项目目录不要过深入，不然会因路径太长导致编译可能失败。
