# 迁移指南



1. 使用 `vue init simulatedgreg/electron-vue my-project` 生成一个崭新的 electron-vue 项目
2. 将当前项目 `src` 内的文件复制到新项目的 `src` 目录中
3. 将 `package.json` 里的依赖关系从当前项目复制到新项目的 `package.json` 里
4. 使用 `yarn` 或 `npm install` 安装依赖
5. 在开发模式下运行项目 (`yarn run dev` 或 `npm run dev`)
6. 监视控制台以修复可能出现的错误

### 问题一

![image-20211215161813067](/Users/bytedance/Library/Application Support/typora-user-images/image-20211215161813067.png)

.electron-vue/webpack.main.config.js添加

```js
externals: [
  ...Object.keys(dependencies || {}),
  {'electron-debug': 'electron-debug'}
],
```



### 问题二 electron中Unable to install vue-devtools的解决方法

由于网络的问题，electron运行的时候加载vue-devtools失败。 Unable to install vue-devtools 。从日志里看retry了四次都timeout了。这个也没有淘宝镜像等国内镜像。不过后来按网上找了个方法，成功加载了最新的版本。

先 npm install vue-devtools --save-dev

然后 把ready事件里面注释掉5行，再加上一行手动加载的。最终src/main/index.dev.js里面修改后的内容如下（所有内容）：

```js
/* eslint-disable */

// Install `electron-debug` with `devtron`
require('electron-debug')({ showDevTools: true })
import {  BrowserWindow } from 'electron';
// Install `vue-devtools`
require('electron').app.on('ready', () => {
  let installExtension = require('electron-devtools-installer')
  // installExtension.default(installExtension.VUEJS_DEVTOOLS)
  //   .then(() => {})
  //  .catch(err => {
  //     console.log('Unable to install `vue-devtools`: \n', err)
  //   })
  //参考 https://www.cnblogs.com/wozho/p/10782654.html 和 https://github.com/SimulatedGREG/electron-vue/issues/242
  BrowserWindow.addDevToolsExtension('node_modules/vue-devtools/vender')  //手动加载vue-devtools，前提是 npm install vue-devtools --save-dev

})

// Require `main` process to boot app
require('./index')
```



问题三，配置vue prettier和eslint冲突

.eslintrc.js添加

```js
    //强制使用单引号
    quotes: ['error', 'single'],
    //强制不使用分号结尾
    semi: ['error', 'never'],
    //末尾不添加逗号，这里没生效可以直接去prettier插件里配置无
    trailingComma: 'none',
```

