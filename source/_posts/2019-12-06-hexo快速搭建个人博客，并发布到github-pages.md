---
title: Hexo快速搭建个人博客，并发布到github pages
catalog: true
comments: true
date: 2019-12-06 10:02:12
subtitle: A succinct hexo theme.
header-img: snail-bg.jpg
tags:
- hexo-theme-snail
- snail
- hexo
catagories:
- hexo-theme-snail
- snail
- hexo
---


## Hexo快速搭建个人博客，并发布到github pages
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

#### 一、全局安装 Git ，Nodejs ， Hexo

##### git及nodejs安装教程 忽略

##### hexo安装
```
$ npm install -g hexo-cli
```
安装完成后在命令行输入 hexo 看一下命令是否存在，如果不存在，可能是需要配置一下环境变量。

#### 二、开始建站
安装完成 hexo 之后执行下面的命令
```
$ hexo init <folder>
$ cd <folder>
$ npm install
```
项目的目录结构如下

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```
##### _config.yml
网站的 [配置](https://hexo.io/zh-cn/docs/configuration) 信息，您可以在此配置大部分的参数。

如果后面要发布到github上 要填写 url 这个参数为github pages 地址
##### package.json
应用程序的信息。
##### scaffolds
模版 文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。

Hexo的模板是指在新建的文章文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。
##### source
资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。
##### themes
主题 文件夹。Hexo 会根据主题来生成静态页面。

#### 三、启动项目

server 指令启动网站项目
```
$ hexo server
```
启动服务器。默认情况下，访问网址为： http://localhost:4000/。

选项 | 描述
---|---
-p, --port | 重设端口
-s, --static | 只使用静态文件
-l, --log | 启动日记记录，使用覆盖记录格式

#### 四、部署项目

首先去github上面创建两个仓库，一个仓库存放本项目，叫地址A，另一个项目存放生成的博客静态文件，叫地址B。

##### 安装 hexo-deployer-git

首先安装 hexo-deployer-git 后面一键发布到github pages要用
```
$ npm install hexo-deployer-git --save
```
然后修改 _config.yml 文件最下面的 deploy 配置：
```
deploy:
  type: git
  repo: <repository url> 地址B
  branch: [branch]
  message: [message]
```

参数 | 	描述 | 默认
---|--- | ---
repo | 库（Repository）地址	| 
branch | 分支名称 |	gh-pages (GitHub) coding-pages (Coding.net) master (others)
message | 自定义提交信息 |	Site updated: {{ "now('YYYY-MM-DD HH:mm:ss')" }})
token |	Optional token value to authenticate with the repo. Prefix with $ to read token from environment variable	

##### 推送本项目
把本项目推到地址A上步骤如下
```
$ git init // git初始化
$ git add . // 添加所有变动
$ git commit -m xxxx // commit 代码
$ git remote add origin 地址A // 本地代码与远程仓库关联
$ git push origin master // 推到远程仓库
```

##### 发布博客

运行下面的命令
清除缓存文件 (db.json) 和已生成的静态文件 (public),并将 public中的博客静态文件推送到地址B上。

```
$ hexo clean && hexo deploy
```
过一会去 地址B 中的setting 的 GitHub Pages 的网址上去查看发布的博客即可。

#### 五、切换主题

[hexo主题](https://hexo.io/themes/)网站上面有很多的主题可以选择，找到选中主题的github地址，切换主题具体操作需要看每个主题的ReadMe即可。如果自己要修改东西也可以进入themes目录下去调整相关代码。