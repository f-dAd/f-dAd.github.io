title: electron
date: 2021-11-03 11:11:32
tags: 

  - electron

categories:

- tools

---

Electron是一个使用 JavaScript、HTML 和 CSS 构建桌面应用程序的框架。 嵌入 [Chromium](https://www.chromium.org/) 和 [Node.js](https://nodejs.org/) 到 二进制的 Electron 允许您保持一个 JavaScript 代码代码库并创建 在Windows上运行的跨平台应用 macOS和Linux——不需要本地开发 经验。

### 搭建electron应用

## Demo01: 搭建一个最简单的 Electron

首先，我们会搭建一个最简单的 Electron 应用，它只有 3 个文件，[点这里](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FWangYuLue%2Felectron-demos%2Ftree%2Fmaster%2Fdemo01)查看Demo01代码

创建 `demo01` 目录：

1、新建 `package.json` 文件

```json
{
  "name": "demo01",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "start": "../node_modules/.bin/electron ."
  }
}
```

2、新建 `index.html` 文件

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using node <script>document.write(process.versions.node)</script>,
    Chrome <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.
  </body>
</html>
```

3、新建 `main.js` 文件

```
const { app, BrowserWindow } = require('electron')

function createWindow () {   
  // 创建浏览器窗口
  let win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // 加载index.html文件
  win.loadFile('index.html')
}

app.whenReady().then(createWindow)
复制代码
```

运行 `yarn start`，第一个 electron 项目就轻松启动起来了。

注意 `package.json` 中的 `main` 字段，它指定了 electron 的入口文件。

在 `main.js` 中，我们注意到，`electron` 模块所提供的功能都是通过命名空间暴露出来的。 比如说： `electron.app` 负责管理 `Electron` 应用程序的生命周期， `electron.BrowserWindow` 类负责创建窗口。

细心的同学注意到，Demo01 其实是 Electorn 官方文档中的[范例](https://link.juejin.cn?target=https%3A%2F%2Fwww.electronjs.org%2Fdocs%2Ftutorial%2Ffirst-app)；是的，官方的范例写的非常简单友好，所以用它来作为我们一系列 Demo 的开始是非常好的选择。

#### 主进程

Electron运行package.json的main脚本的进程被称为主进程
每个应用只有一-个主进程
管理原生GUI,典型的窗口( BrowserWindow、Tray、Dock、Menu)
创建渲染进程
控制应用生命周期(app)



#### 渲染进程

展示Web页面的进程称为渲染进程， 同web 但web是运行在沙盒中的，这个可以利用node访问系统
通过Node.js、Electron 提供的API可以跟系统底层打交道
一个Electron应用可以有多个渲染进程.

![image-20211206111042495](/Users/bytedance/Library/Application Support/typora-user-images/image-20211206111042495.png)

#### Electron进程间通信的目的

通知事件
数据传输
共享数据

#### IPC模块通信

Electron 提供了IPC通信模块，主进程的ipcMain和渲染进程的ipcRenderer
ipcMain、ipcRenderer 都是EventEmitter对象

#### 进程间通信:从渲染进程到主进程

Callback写法:
	ipcRenderer,send (channel, ..args)
	ipcMain.on(channel, handler)
Promise写法(Electron 7.0之后，处理请求+响应模式)
	ipcRenderer.invoke(channel, ..args)
	ipcMain.handle(channel, handler)

#### 进程间通信:从主进程到渲染进程

主进程通知渲染进程:
	ipcRenderer.on(channel, handler)
	webContents.send(channel) 一个主进程，多个渲染进程，需要找到具体的渲染进程

#### 页面间( 渲染进程与渲染进程间)通信

通知事件
	通过主进程转发(Electron 5之前)
	ipcRenderer.sendTo (Electron 5之后)
数据共享
	Web 技术( localStorage、sessionStorage、 indexedDB )
	使用remote 不推荐用不好可能导致程序卡顿，性能不好

#### 经验&技巧

少用remote模块  会触发底层的同步ipc事件，特别影响性能
不要用sync模式  可能导致程序卡死
在请求+响应的通信模式下，需要自定义超时限制

![image-20211206112648529](/Users/bytedance/Library/Application Support/typora-user-images/image-20211206112648529.png)

#### 使用 Electron API 创建原生 GUI

BrowserWindow 应用窗口
Tray托盘
app iX Ïdock.badge
Menu菜单
dialog ESTE
TouchBar苹果触控栏.....

#### 使用Electron API获得底层能力

clipboard剪切板
globalShortcut 全局快捷键
desktopCapture捕获桌面
shell打开文件、URL....



#### 使用Node.js获得底层能力

Electron同时在主进程和渲染进程中对Node.js暴露了所有的接口
	fs进行文件读写.
	crypto进行加解密
通过npm安装即可引入社区上所有的Node.js库



#### 使用Node.js调用原生模块

node.js add-on
node-ffi (Foreign Function Interface)

#### 调用OS能力

WinRT(https:// github.com/ NodeRT/NodeRT)
Applescript (https:ll github.com/TooTallNate/ node- applescript)
Shell (node.js child_ process)

![image-20211206113346000](/Users/bytedance/Library/Application Support/typora-user-images/image-20211206113346000.png)

#### 无兼容问题

不用担心在Safari、IE. 上的表现差异了大胆使用Chrome浏览器已经支持的API
babel中设置targets为Electron对应的Chrome版本

​	ES 6/718/9/ 10高级语法 均可使用
​		Async await / Promise
​		String/Array/Object等高级用法
​		BigInt

#### 无跨域问题

使用Node.js发送请求
使用Electron net发送请求

操作本地文件

更好用的本地db

多线程、多进程并行







实战，远程控制软件

![image-20211206114624981](/Users/bytedance/Library/Application Support/typora-user-images/image-20211206114624981.png)



### 技术关键点

#### 1.怎么捕获画面?

##### Electron desktopCapturer

desktopCapturer
通过[ navigator. mediaDevices. getUserMedia ] API，可以访问那些用于从桌面上捕获音频和视频的媒体源信息。
进程: Renderer

#### 2.怎么完成用户间连接、画面+指令传输?

WebRTC  客户端点对点连接方案
Web Real-Time Communications

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20211206114842064.png" alt="image-20211206114842064" style="zoom:50%;" />

#### 3.怎么响应控制指令?

robotjs(Node.js)  node的c++扩展，实现鼠标滚动点击等效果

### 目录架构

common存放渲染进程、主进程可复用代码
前端框架在render/src/页面，构建产物在pages/页面
纯JS直接在Pages页面下

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20211206115213602.png" alt="image-20211206115213602" style="zoom:50%;" />

### 与React框架结合

#### 跟Electron在一起工作要做些什么呢?

●书写React， 并且编译它。CRA其实-个好的选择。
●处理引入electron/node模块:
●Webpack配置: https://webpack.js.org/ configuration/target/
●window.require
●Windows根据环境信息加载本地或者devServer url
●electron-is-dev
●启动命令适配。等到编译成功再启动ELectron
●concurently
●wait on

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20211208114257411.png" alt="image-20211208114257411" style="zoom:33%;" />

如何捕获媒体流?
navigator.mediaDevices.getUserMedia ( MediaStreamConstraints)
返回: Promise, 成功后 resolve回调一个MediaStream 实例对象
参数: MediaStreamConstraints
. audio: Booleanl MediaTrackConstraints
. video: Boolean I MediaTrackConstraints
     width: 分辨率
.    height:分辨率
.     frameRate: 帧率 比如{ideal:10,max:15}

例子：捕获音视频流媒体
navigator.mediaDevices.getUserMedia(l
	audio: true,
	video:{}
	width:{min: 1024, ideal: 1280, max: 1920},
	height: {min: 576, ideal: 720, max: 1080},
	frameRate: {max: 30}  
})

播放

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20211208173636557.png" alt="image-20211208173636557" style="zoom:25%;" />

<img src="/Users/bytedance/Library/Application Support/typora-user-images/image-20211208171916427.png" alt="image-20211208171916427" style="zoom: 50%;" />

![image-20211208173744544](/Users/bytedance/Library/Application Support/typora-user-images/image-20211208173744544.png)







![image-20211208201939841](/Users/bytedance/Library/Application Support/typora-user-images/image-20211208201939841.png)

![image-20211208202006331](/Users/bytedance/Library/Application Support/typora-user-images/image-20211208202006331.png)





![image-20211208205220046](/Users/bytedance/Library/Application Support/typora-user-images/image-20211208205220046.png)





![image-20211213154942433](/Users/bytedance/Library/Application Support/typora-user-images/image-20211213154942433.png)





![image-20211213155002883](/Users/bytedance/Library/Application Support/typora-user-images/image-20211213155002883.png)

![image-20211213155615937](/Users/bytedance/Library/Application Support/typora-user-images/image-20211213155615937.png)

![image-20211213180113502](/Users/bytedance/Library/Application Support/typora-user-images/image-20211213180113502.png)





![image-20211213205327123](/Users/bytedance/Library/Application Support/typora-user-images/image-20211213205327123.png)





![image-20211213205349930](/Users/bytedance/Library/Application Support/typora-user-images/image-20211213205349930.png)

![image-20211213205524224](/Users/bytedance/Library/Application Support/typora-user-images/image-20211213205524224.png)
