# 鸿蒙Taro实战：01-搭建开发环境

## 配置鸿蒙环境

### 下载安装 DevEco

### 配置IDE

打开 `Prefreences`, `OpenHarmony SDK`, 勾选 `API Version 12`

## 创建鸿蒙项目

打开 DevEco，点击 右上角`Create Project`， 在 `Application` 处选择 `Empty Ablity`, 点击 `Next`, 进入配置页，根据需求调整内容，这里使用默认配置，

1. Project name: `MyApplication`,
2. Bundle name: `com.example.myapplication`,
3. Save location 选择需要创建的目录，这里使用 MyApplication 目录 （~/projects/MyApplication)
4. Compatible SDK, 选择 **4.0.0**
5. Module name: entry

注意，上面当前 Taro 支持的 SDK 版本为 4.0.0

点击 `Finish` 完成项目创建。

## 安装 Taro 4.x

```bash
yarn global add @tarojs/cli
```

安装成功后检查 `taro` 是否生效

```bash
➜  ~ taro --version
👽 Taro v4.0.7

4.0.7
```

## 初始化项目

```bash
taro init taro-ohos
```

按照提示输入，这里使用以下配置

```bash
? 请输入项目介绍 taro ohos
? 请选择框架 React
? 是否需要使用 TypeScript ？ Yes
? 请选择 CSS 预处理器（Sass/Less/Stylus） Sass
? 请选择包管理工具 yarn
? 请选择编译工具 Vite
? 请选择模板源 Gitee（最快）
✔ 拉取远程模板仓库成功！
? 请选择模板 默认模板
```

等待项目创建成功，直到输出以下提示：

```bash
Done in 44.95s.
✔ 安装项目依赖成功
创建项目 taro-ohos 成功！
请进入项目目录 taro-ohos 开始工作吧！😝
```

### 安装鸿蒙插件

```bash
yarn add @tarojs/plugin-platform-harmony-ets
yarn add path
```

### 修改编译配置

找到 `config/index.ts` 文件, 在 plugin 处添加 `@tarojs/plugin-platform-harmony-ets`, 在 `rn` 下方添加 harmony 配置：

```bash

import path from 'path'

...

   ...
    plugins: [
      '@tarojs/plugin-platform-harmony-ets'
    ],
    ...
    rn: {...},
    harmony: {
        // 将编译方式设置为使用 Vite 编译
        compiler: 'vite',
        // 【必填】鸿蒙主应用的绝对路径，例如：
        projectPath: path.resolve(process.cwd(), '../MyApplication'),
        // 【可选】HAP 的名称，默认为 'entry'
        hapName: 'entry',
        // 【可选】modules 的入口名称，默认为 'default'
        name: 'default',
    }
```

注意这里要把 projectPath 设置成 Deveco 创建的鸿蒙项目目录

### 修改 package.json

在 scripts 处添加以下配置

```json
"scripts": {
    ...
    "build:harmony": "taro build --type harmony",
    "dev:harmony": "npm run build:harmony -- --watch"
}
```

### 运行 Taro 项目

```bash
yarn run dev:harmony
```

控制台输出以下内容，显示构建成功：

<details>

<summary>build started... 点击查看完整输出
</summary>

```bash
yarn run v1.22.22
$ npm run build:harmony -- --watch

> taro-ohos@1.0.0 build:harmony
> taro build --type harmony --watch

👽 Taro v4.0.7

watching for file changes...

build started...
✓ 7 modules transformed.
rendering chunks (6)...

开始 ohpm install 脚本执行...

install completed in 0s 36ms
执行 ohpm install 脚本成功。

../MyApplication/entry/src/main/ets/app.scss.xss.js                 0.10 kB │ gzip: 0.10 kB │ map: 0.10 kB
../MyApplication/entry/src/main/ets/index.scss.xss.js               0.10 kB │ gzip: 0.10 kB │ map: 0.10 kB
../MyApplication/entry/src/main/ets/app_comp.js                     0.27 kB │ gzip: 0.21 kB │ map: 0.70 kB
../MyApplication/entry/src/main/ets/pages/index/index_taro_comp.js  0.40 kB │ gzip: 0.27 kB │ map: 0.11 kB
../MyApplication/entry/src/main/ets/app_taro_comp.js                0.83 kB │ gzip: 0.46 kB │ map: 0.13 kB
../MyApplication/entry/src/main/ets/pages/index/index_comp.js       0.89 kB │ gzip: 0.42 kB │ map: 0.99 kB
../MyApplication/entry/src/main/ets/app.ets                         2.21 kB │ gzip: 0.86 kB
../MyApplication/entry/src/main/ets/render.ets                      5.76 kB │ gzip: 1.23 kB
../MyApplication/entry/src/main/ets/pages/index/index.ets           9.04 kB │ gzip: 2.44 kB
built in 2489ms.
```

</details>

<br/>

Taro 会将编译好的文件输出至鸿蒙项目目录

### 运行鸿蒙

1. 配置应用签名

打开 `File` -> `Project Structure...`, 点击 `Siging Configs`, `Sign In`, 例如华为账号，点击右下角 `Apply`, `OK`, 完成签名

2. 运行

 在 DevEcho 中，点击运行按钮，待控制台执行完成，查看设备上，页面中将输出以下内容

 ```bash
   首页

 Hello world!
 ```

## 注意事项

1. 运行 Taro 时报错 `throw new Error(`不存在编译平台 ${platform}`)`，config/index.ts文件中没有添加 `@tarojs/plugin-platform-harmony-ets`
2. 当前开发方式不支持 Hot reload

## 参考资料

- [鸿蒙 & OpenHarmony](https://docs.taro.zone/docs/next/harmony/)
