title: Github 加 hexo 搭建自己的博客
date: 2013-12-24 17:49:32
tags:

---

## 安装

git：git 可以在我们的电脑上创建一个本地仓库，然后把我们的网页部署到 github 上的 github Pages 上。

node.js:安装 Hexo 的前提。

Hexo：可以把我们本地的 Markdown 文件，后缀.md 的文件，处理，渲染，然后变成静态网页，也就是我们的博客

```bash
npm config set registry https://registry.npm.taobao.org 设置国内的淘宝镜像，其他镜像也可以`
npm config get registry  检查换成功没

安装Hexo
npm install hexo-cli -g

安装hexo的一个相当于命令的东西
npm install hexo-deployer-git --savecode snippet
```

### 搭建博客

```bash
创建文件夹，并进去
mkdir -p ~/codes/myblog && cd ~/codes/myblog
hexo init 初始化
启动 访问localhost:4000 博客雏形就出来了
hexo s         默认端口4000

Hexo 命令
npm update hexo -g #升级
命令简写
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo g == hexo generate #生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy #部署
hexo server #Hexo会监视文件变动并自动更新，无须重启服务器
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP
hexo clean #清除缓存，若是网页正常情况下可以忽略这条命令
```

## github 配置

### 配置 ssh key

```bash
ssh-keygen -t rsa -C "邮箱@example.com"
ssh-keygen -t rsa -C "邮箱@example.com" -f ~/.ssh/前缀id_rsa  防止和默认名重复

ssh-add ~/.ssh/前缀_id_rsa       添加到信任列表
pbcopy < ~/.ssh/前缀_id_rsa.pub  复制公钥，粘贴到git网站的公钥中去
open ~/.ssh/ 配置新账号，多git账号时使用

添加下面的内容
#个人2
Host blog
Hostname gihub.com
IdentityFile ~/.ssh/github_id_rsa
User blog

ssh -T git@blog 测试连接
```

### 创建仓库

> 注意：填写 Repository name，格式为 xxx.github.io，xxx 就是你的用户名，xxx 必须和你的用户名匹配，一个账户只能在 Github 上创建一个 blog 仓库。

![image-20211101184859389](/Users/bytedance/Library/Application Support/typora-user-images/image-20211101184859389.png)

### **推送网站**

在 blog 根目录里的\_config.yml 文件称为**站点**配置文件

根目录里的 themes 文件夹，里面也有个\_config.yml 文件，这个称为**主题**配置文件

将 Hexo 与 GitHub 关联起来，打开站点的配置文件\_config.yml，翻到最后修改为：

```yml
deploy:
	type: git
	repo: git@github.com: ***/chenQD-blog.git
	branch: master
```

记住这有个坑，

这里不要使用 https 链接，要不 hexo deploy 时重复输入用户名密码的问题

```bash
hexo clean
hexo g
hexo d
或者
hexo generate -deploy  生成+部署
```

如果报错：说明最开始安装的 deploy 没成功，会也有提示（会让你安装）： ERROR Deployer not found: git

只需要安装好即可

```bash
npm install hexo-deployer-git --save
```

当配置完\_config.yml 文件时，执行 hexo g -d 时出现错误如下：上面的配置文件格式缩进不正确，报错

```bash
FATAL YAMLException: bad indentation of a mapping entry (110:23)
 107 |
 108 | deploy:
 109 |   type: git
 110 |   repo: git@github.com: f-dAd/f-dAd.github.io.git
-----------------------------^
```

### 博客样式

可以去 hexo 官网去挑选样式，下面几个是我很喜欢的样式

[Claudia](https://github.com/Haojen/hexo-theme-Claudia) ，[icalm](https://github.com/nameoverflow/hexo-theme-icalm)，[Keep](https://github.com/XPoet/hexo-theme-keep)，[Archer](https://github.com/fi3ework/hexo-theme-archer)

找到对应博客样式的 github 按教程安装

简单方式：直接 git clone 一份别人配置好的 themes 文件夹直接替换自己的文件夹并，在\_config.yml 文件里把主题换成 themes 文件夹里对应的主题问件夹名

hexo generate -deploy 部署

### 备份博客源文件

有时候我们想换一台电脑继续写博客，这时候就可以将博客目录下的所有源文件都上传到 github 上面。

首先在 github 博客仓库下新建一个分支`hexo`，然后`git clone`到本地一个新建的文件夹，把`.git`文件夹拿出来，放在博客根目录下。cmd + shift + . 显示隐藏文件夹

```bash
git branch -b hexo   切换到`hexo`分支
git add .
git commit -m "xxx"
git push origin hexo  提交就行了
```

### 代码高亮

Hexo 本身就自带高亮功能，只不过不完美。话虽如此，这个自带的高亮功能在我所用的[主题](https://github.com/chpwang/hexo-theme-pln)里也比市场上的其他插件（例如 [Hexo-Prism-Plugin](https://github.com/ele828/hexo-prism-plugin) ，[hexo-filter-highlight](https://github.com/Jamling/hexo-filter-highlight) 和 [Prettify](https://github.com/google/code-prettify) 这三个坑货）要好用得多。这里说的好用，主要体现在最终渲染出来的排版效果上的美观、设置上的便捷，且不会引入过多 Markdown 和 Latex 之间语法的冲突。

经过一番调研，最终我放弃安装 Plugin（插件）版本的 Highlight.js（ [hexo-filter-highlight](https://github.com/Jamling/hexo-filter-highlight) 就是一个基于 Highlight.js 开发的 Plugin 。这些 Plugin 虽然安装设置方便，其模块化属性也方便管理，但如果效果不好，不如不用），直接选择使用原版的 [Highlight.js](https://highlightjs.org/) 。你可以在[官方 Demo ](https://highlightjs.org/static/demo/)页面查看它各个语言在各个风格（Style）下的显示效果。

---

### 实现

#### 步骤一：

前往 Highlight.js 的[官方下载页面](https://highlightjs.org/download/)，在 **Custom package** 的部分勾选你希望获得高亮支持的语言（想一步到位就全选），勾选完毕后点击 Download 按钮下载，得到 `highlight.zip` 压缩包；

#### 步骤二：

解压刚刚的 `highlight.zip` 压缩包，得到 `highlight.pack.js` 文件和 `styles`文件夹（该文件夹中包含了各种[显示风格](https://highlightjs.org/static/demo/)的 CSS 文件）。接着，将 `highlight.pack.js` 文件移动到 `themes/hexo-theme-Claudia/source/js/` 目录下，而 `styles` 文件夹（包括里面的所有 CSS 文件）则移动到 `themes/hexo-theme-Claudia/source/style/themes` 目录下；

#### 步骤三：

修改根目录下 `_config.yml` 文件中 highlight 部分的设置（主要目的是关闭它，其他设置只是顺便说明一下）：

```
highlight:
  enable: false 关闭 hexo 自带的 highlight*
  line_number: false
  auto_detect: false
  tab_replace: ""
  wrap: false
hljs:
  enable: true #true to enable the plugin
  line_number: frontend # add line_number in frontend or backend (not recommend, have bugs in special hexo version)  没生效
  trim_indent: backend # trim the indent of code block to prettify output. backend or front-end (recommend)
  copy_code: true # show copy code in caption.
  label:
    left: Code
    right: ":"
    copy: copy 这里赋制按钮没用
```

把 vs2015.css 粘贴到改 `themes/hexo-theme-Claudia/source/style/themes/highlight-theme-light.css` ，修改

`themes/hexo-theme-Claudia/layout/post.pug`（通过调用指定的 CSS 文件选择相应的高亮风格，下面的例子里选择的风格是 [Vs 2015](https://highlightjs.org/static/demo/)）：

```pug
link(rel='stylesheet', href= url_for('/style/themes/vs2015.min.css'))
```

