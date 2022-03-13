---
title: Hexo搭建blog记录
date: 2019-07-22 13:03:52
categories:
- hexo
tags:
- hexo
---

使用Hexo搭建个人博客，并通过github进行发布。该搭建过程是在windows上完成的，除开部分权限，与Linux上完全一样。
<!--more-->

# Windows
## 一、准备工作
#### 安装nodejs
1. 官网下载系统对应的node.js安装包（windows64位的msi文件)，按照提示一步步安装。
2. 安装完成后再cmd中可以通过`node -v`和`nmp -v`分别查看node.js和nmp是否安装成功。
3. 如果盘够大，跳到第5步。(windows下)可以设置文件夹node_global和node_cache放置全模块所在路径和缓存路径。运行以下：
```    
	npm config set prefix"node_global目录"
	npm config set cache"node_cache目录"
```

4. 设置环境变量：
系统变量添加 变量名：`NODE_PATH` 变量值：`node_global目录\node_modules`；
用户变量PATH中将npm路径换成`node_global路径`以及添加`nodejs路径`。
此时使用npm安装的文件都会放入对应的文件夹。
5. 由于国内镜像很慢，所以可以安装淘宝镜像 `npm install -g cnpm --registry=https://registry.npm.taobao.org`


#### 安装git
1. 官网下载安装，安装完成后，会有git.bash、git.gui和git.cmd，个人习惯git.bash，和linux命令完全一致。
2. 为防止后续权限不足，更改git.bash权限为管理员
3. 配置全局用户名和邮箱（好像可以与github用户名邮箱不同，没有测试过，个人习惯相同）：
```
git config global user.name"github name"
git config global user.email"email"
```

4. 为使用SSH进行push等，需要申请SSH key。查看`C:\user\user name\.ssh`目录下是否有id_rsa和id_rsa.pub文件，如果有，下一步，如果没有，在当前文件夹下打开git.bash，创建SSH key：`ssh-keygen -t rsa C "youremail"`
5. 打开github页面，将id_rsa.pub文件内容复制进`settings->SSH and GPG keys->New SSH key`中添加新的ssh keys。然后就可以本地进行git clone等操作。（会有警告，然后输入yes就可以了）


## 二、安装hexo
1. 使用cnmp安装hexo博客框架。
打开git.bash，运行`cnpm install -g hexo-cli`安装hexo，使用`hexo -v`验证。

2. 在一个文件夹下，使用`hexo init`初始化框架（管理员权限)。
3. `hexo g`生成静态文件。
4. `hexo s`启动服务，通过本地端口可以查看博客网页。
5. `hexo n "blog title"`新建一个blog，然后编辑该md文件。

更多内容后续研究。。。


## 三、部署在git上
1. 在个人github中新建repo名为`githubname.github.io`，注意githubname一定要是github的用户名。并复制对应的SSH值。
2. 在blog文件夹中安装git部署插件：`cnpm install --save hexo-deployer-git`。
3. 编辑本地blog文件夹下`_config.yml`文件中属性值：
```
type: git
repo: ssh value you coped
branch: master
```
注意，windows下冒号后有一个空格；也可以使用https访问，但每次都需要输入用户名和密码，比较麻烦。

4. 在生成静态文件后，使用`hexo d`进行部署，然后就可以使用`githubname.github.io`域名来访问个人博客。



*根据视频 [手把手教你从0开始搭建自己的个人博客 |无坑版视频教程| hexo](https://www.bilibili.com/video/av44544186?from=search&seid=14183335241576131046)  以及 [Windows下通过GitHub+Hexo搭建个人博客的步骤](https://blog.csdn.net/weixin_39345384/article/details/80095883) 学习记录。*


# Mac
## Preparation
+ install npm `brew install node.js`
	+ `npm -v` to check version
+ install hexo cli `npm install -g hexo-cli`

## Init hexo
+ `hexo init` init hexo
+ `hexo g` generate files
+ `hexo s` run server
+ `hexo clean` clean generated files
+ `hexo d` deploy to remote

## 

2019.08.03 add

## 添加blog对图片的支持
1. 更改配置文件`_config.yml`中`post_asset_folder`为true。
2. ~~在**blog文件夹**下安装图片支持插件`npm install hexo-asset-image --save`。~~
2. 安装`npm install hexo-image-link --save`
3. 建立新文档xxx，在对应的文件夹中放置图片等文件，然后md文件中通过`![](xxx/image.png)`方式引入图片。
4. hexo clean、hexo g、hexo s即可查看；hexo d即可push入git，网页也能看到图片。

2022.03.04 add

## 支持数学公式
+ `npm uninstall hexo-renderer-marked`
+ `npm install hexo-renderer-pandoc`
+  enable mathjax in `_config.yml`
```
math:
  ...
  mathjax:
    enable: true
```
+ enable mathjax for each file need to display equation

> https://github.com/theme-next/hexo-theme-next/blob/master/docs/MATH.md
