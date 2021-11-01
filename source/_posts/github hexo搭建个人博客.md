title: Github加hexo 搭建自己的博客
date: 2013-12-24 17:49:32
tags:

---

## 安装

git：git可以在我们的电脑上创建一个本地仓库，然后把我们的网页部署到github上的github Pages上。

node.js:安装Hexo的前提。

Hexo：可以把我们本地的Markdown文件，后缀.md的文件，处理，渲染，然后变成静态网页，也就是我们的博客。

```bash
npm config set registry https://registry.npm.taobao.org 设置国内的淘宝镜像，其他镜像也可以

npm config get registry//**这个是用来检查换成功没**

安装Hexo
npm install hexo-cli -g

安装hexo的一个相当于命令的东西
npm install hexo-deployer-git --save
```

## 搭建博客

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

## github配置

### 配置ssh key

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

> 注意：填写Repository name，格式为xxx.github.io，xxx就是你的用户名，xxx必须和你的用户名匹配，一个账户只能在Github上创建一个blog仓库。

![img](https://bytedance.feishu.cn/space/api/box/stream/download/asynccode/?code=NWViY2FhOTYxOGQ2NmJkMzE5NmFiODc1OTkxMzc5Y2RfV1pUSmtVcnV2UTQyeUZBSGxWdEY0aXFZV09GcXVpQ1VfVG9rZW46Ym94Y253NGJrWlpPU0RJcmZOeXprS0dQU1RjXzE2MzU3NTU0NzU6MTYzNTc1OTA3NV9WNA)

### **推送网站**

在blog根目录里的_config.yml文件称为**站点**配置文件

根目录里的themes文件夹，里面也有个_config.yml文件，这个称为**主题**配置文件

将Hexo与GitHub关联起来，打开站点的配置文件_config.yml，翻到最后修改为：



```
deploy:
	type: git
	repo: git@github.com: ***/chenQD-blog.git 
	branch: master
```

记住这有个坑，

 这里不要使用https链接，要不hexo deploy时重复输入用户名密码的问题



```bash
hexo clean 
hexo g 
hexo d   
或者
hexo generate -deploy  生成+部署
```

如果报错：说明最开始安装的deploy没成功，会也有提示（会让你安装）：  ERROR Deployer not found: git

只需要安装好即可

```
npm install hexo-deployer-git --save
```

当配置完_config.yml文件时，执行hexo g -d时出现错误如下：上面的配置文件格式缩进不正确，报错

```bash
FATAL YAMLException: bad indentation of a mapping entry (110:23)
 107 |
 108 | deploy:
 109 |   type: git
 110 |   repo: git@github.com: f-dAd/f-dAd.github.io.git
-----------------------------^
```

### 博客样式

可以去hexo官网去挑选样式，下面几个是我很喜欢的样式

[Claudia](https://github.com/Haojen/hexo-theme-Claudia) ，[icalm](https://github.com/nameoverflow/hexo-theme-icalm)，[Keep](https://github.com/XPoet/hexo-theme-keep)，[Archer](https://github.com/fi3ework/hexo-theme-archer)

找到对应博客样式的github 按教程安装

简单方式：直接git clone一份别人配置好的themes文件夹直接替换自己的文件夹并，在_config.yml文件里把主题换成themes文件夹里对应的主题问件夹名

hexo generate -deploy 部署

### 备份博客源文件

有时候我们想换一台电脑继续写博客，这时候就可以将博客目录下的所有源文件都上传到github上面。

首先在github博客仓库下新建一个分支`hexo`，然后`git clone`到本地一个新建的文件夹，把`.git`文件夹拿出来，放在博客根目录下。cmd + shift + . 显示隐藏文件夹

```bash
git branch -b hexo   切换到`hexo`分支
git add .
git commit -m "xxx"
git push origin hexo  提交就行了
```

