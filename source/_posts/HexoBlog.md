---
title: 使用Github的Pages服务搭建Hexo静态博客
date: 2016/5/18 
categories:
- 技术分享
tags:
- Github
- Hexo
- 静态博客
---


## Hexo介绍 ##

Hexo是使用Node.js实现的快速、间接且高效的博客框架。
Hexo的特点

- 超快速度：Node.js 所带来的超快生成速度，让上百个页面在几秒内瞬间完成渲染。
- 支持MarkDown：Hexo 支持 GitHub Flavored Markdown 的所有功能，甚至可以整合 Octopress 的大多数插件。
- 一键部署：只需一条指令即可部署到 GitHub Pages, Heroku 或其他网站。
- 丰富插件：Hexo 拥有强大的插件系统，安装插件可以让 Hexo 支持 Jade, CoffeeScript。
Hexo的官网：[Hexo](https://hexo.io/zh-cn/)

<!--more-->

## 工具与环境 ##
- <a href="https://nodejs.org/en" rel="nofollow">Node.js</a>
无脑下一步安装，命令行测试`node`和`npm`指令
- <a href='http://git-scm.com' rel='external nofollow'>Git</a>
Git的安装也很简单粗暴，一路下一步。
	
- 如若想利用Github的Pages服务搭建博客则建立名为`yourusernam.github.io`的仓库，使用默认`master`分支即可。
- 若是想利用Coding(Gitcafe已被其收购)的Pages服务搭建博客则建立名为`yourusernam`的仓库，使用`coding-pages`分支。
>仓库命名均有严格规范，不可更改

## 先介绍常用命令 ##
```
hexo generate = hexo g  //生成静态博客，即重新生成public文件夹
hexo server = hexo s  //本地预览调试 (一般组合使用 hexo g && hexo s)
hexo delopy = hexo d //(发布到github)
hexo new = hexo n //(执行该命令之后在source_posts目录下产生< blog name >.md文件，这是你的一篇新博客。可以在markdown工具下编写)
hexo 
hexo clean //清除缓存,一般本地服务器效果有了但public文件没变化，则删除根目录的db.json再重新生成部署
hexo d -g   //hexo d 和hexo g 的合并，生成并部署
```

## 安装Hexo ##
#### 安装Hexo到本地 ####
	1. 在桌面下使用GIT Bash输入下面代码，实现Hexo的安装
`$ sudo npm install hexo-cli -g`
	2. 命令行直接输入
`npm install hexo-cli -g`

#### 初始化自己的个人博客 ####
	使用GIT Bash切换到你希望安装个人博客的文件夹下,输入命令：
	`$ hexo init folderName`
	folderName是你希望存放博客文件的文件夹名称

## 主题的安装 ##
在完成上一步后，其实就可以运行查看页面了，这一步让你实现安装自己喜欢的主题，可以在Github网站中输入hexo关键字，可以搜索到很多相关主题，挑选一个就可以了

我这里使用NEXT这个主题进行讲解，其他的方式也是一样的。

使用GIT Bash切换到你刚才个人博客安装目录
```
$ cd <blogFolder>   //切换到博客的安装目录
$ git clone https://github.com/iissnan/hexo-theme-next themes/next  
```
完成上面步骤，会在theme的目录下多了一个next文件，说明主题下载成功了，但现在还没有配置完成

在`username.github.io`文件下有一个`_config.yml`文件，编辑它

将`theme`的`value`设为自己的主题名，这里是next——完成主题的配置

将`deploy`的`value`进行配置——完成主题发布地址配置:
```
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: master
```
> 这里可以添加多个仓库，repo：仓库名,分支名

## 创建文章 ##
在`username.github.io/source/_posts`目录下创建你自己的文章，文章格式为Markdown的.md格式
```
---
title: Hexo让博客梦变的简单
date: 2016-05-12
---
title:文章名，date:发布的日期
```
## 发布到Github ##
安装自动部署发布工具

使用GIT Bash切换到username.github.io目录下
```
$ npm install hexo-deployer-git --save
编译

$ hexo generate
本地测试

$ hexo s -p 4000
-p:设置端口号，默认为4000，使用默认端口可以省略
```
本地测试没有问题后就可`hexo d -g`部署到github或者coding上面了

