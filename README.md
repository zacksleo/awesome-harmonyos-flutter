# awesome-harmonyos-flutter


## Mac 安装

环境变量配置

```
# Flutter Mirror
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

# HarmonyOS SDK
export TOOL_HOME=/Applications/DevEco-Studio.app/Contents/
export DEVECO_SDK_HOME=$TOOL_HOME/sdk # command-line-tools/sdk
export PATH=$TOOL_HOME/tools/ohpm/bin:$PATH # command-line-tools/ohpm/bin
export PATH=$TOOL_HOME/tools/hvigor/bin:$PATH # command-line-tools/hvigor/bin
export PATH=$TOOL_HOME/tools/node/bin:$PATH # command-line-tools/tool/node/bin
```

## Windows 安装


配置用户变量


FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

PUB_HOSTED_URL=https://pub.flutter-io.cn

DEVECO_SDK_HOME=C:\Program Files\Huawei\DevEco Studio\sdk


配置环境变量

编辑 PATH，添加以下路径

C:\Program Files\Huawei\DevEco Studio\tools\ohpm\bin

C:\Program Files\Huawei\DevEco Studio\tools\hvigor\bin

C:\Program Files\Huawei\DevEco Studio\tools\node

## 常见问题

1. 运行 flutter doctor 出现 `Error: Unable to find git in your PATH.`

执行以下命令


```
git config --global --add safe.directory '*'
```