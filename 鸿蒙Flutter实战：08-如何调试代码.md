# 鸿蒙Flutter实战：如何调试代码

1.环境搭建
2.配置
3.查看日志
4.调试

主要有两种调试方案。

方案一
在IDE 中直接运行Flutter项目，IDE可选择Andriod Studio或者VsCode，在调试栏点击Debug运行。


方案二
适应DecEco运行鸿蒙项目，注意需要打开的是ohos鸿蒙目录代码，待IDE分析结束后，点击运行。

当app在鸿蒙设备上启动成功后，立即在Vscode中调出Command Pallet，找到Flutter Attach ，将Flutter调试器连接至宿主机

然后就是增加断电，使用hot reload重新加载Flutter，调试项目代码。