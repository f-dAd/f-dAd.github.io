# 迁移指南

### electron-vue

[electron-vue](https://link.segmentfault.com/?enc=3I0pzpx4kRStAZ1pO62wmQ%3D%3D.7jGEAOdounF8o6Iv5Lwx%2FL17CQzCZie0ZeTtXpxSLo9sWtFknA7%2BrfclHmmMUavu) 类似于 Vue 脚手架，只需要在规定的文件夹里写代码就行，使用中的问题：

- 感觉脚手架作者不怎么维护。

- Vue 和其他依赖比较旧，直接改版本出不出问题不好说。

- 如果在现有的 Vue 项目中引入 `Electron`，还得来回拷贝，报错改错。

  

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



### 问题三 配置vue prettier和eslint冲突

.eslintrc.js添加

```js
    //强制使用单引号
    quotes: ['error', 'single'],
    //强制不使用分号结尾
    semi: ['error', 'never'],
    //末尾不添加逗号，这里没生效可以直接去prettier插件里配置无
    trailingComma: 'none',
```





## 新版方案

### vue-cli-plugin-electron-builder

[vue-cli-plugin-electron-builder](https://link.segmentfault.com/?enc=PEsFAhdtcl9ke5l2H%2F5mZw%3D%3D.zx6m%2B7O87XJ0%2BouUURIX8wSGyn6t2RTrkp4XKEG48S3x%2FLF8vNWXmq9jSSIaONHjQSjddMjgu37c9ly2SHgL6A%3D%3D) 相对于 `electron-vue` 灵活了许多，更有在 Vue 项目中集成 `Electron` 的感觉，因此这篇博客主要说明 `vue-cli-plugin-electron-builder` 的使用。这里列出几个个人认为的优点：

- 目录结构基本按照 Vue 脚手架的结构。
- 非侵入式，依赖版本的管理与 一般的 Vue 应用管理相同。
- 以 `Vue CLI` 插件的形式安装，命令统一使用 `vue-cli-service` ，[Vue CLI 插件](https://link.segmentfault.com/?enc=a2DxEnOmD%2F0%2BtbvCu9hPhg%3D%3D.jJMyN81EfJ%2BAUds%2BPl7V5TxpTY0WFMOOiSsEIkUCHFRn1%2B%2BvWOpUzo0NVMdpVxwwB2u6YKq34JJ0RmbU%2B1%2BKklhhqUeFUdNijnvp31yY094%3D)。

