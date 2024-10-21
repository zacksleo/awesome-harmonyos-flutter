# 鸿蒙Flutter实战：混合开发

鸿蒙Flutter混合开发主要有两种形式。

## 1.基于har

将flutter module打包成har包，在原生鸿蒙项目中，以har包的方式引入。

其优点是主项目开发者可以不关注Flutter实现，不需要安装配置Flutter开发环境，缺点是无法及时修改Flutter代码，也不存在热重载。

## 2.基于源码

通过源码依赖的当时，在原生鸿蒙项目处，引入Flutter模块。

其优点是方便维护和更新Flutter代码，也可以使用热重载。缺点是需要搭建Flutter开发环境，开发人员需要掌握Flutter。

## 参考资料

