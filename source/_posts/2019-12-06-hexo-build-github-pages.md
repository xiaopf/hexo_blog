---
title: Hexo快速搭建个人博客，并发布到github pages
catalog: true
comments: true
date: 2019-12-06 10:02:12
subtitle: 快速搭建hexo个人博客
header-img: defaultHeaderBg.jpg
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


generate 指令生成静态文件

```
$ hexo generate --watch //参数是监控文件变动自动生成静态文件的
```

选项 | 描述
---|---
-d, --deploy | 文件生成后立即部署网站
-w, --watch | 监视文件变动
-b, --bail | 启动日记记录，使用覆盖记录格式
-f, --force | 生成过程中如果发生任何未处理的异常则抛出异常存在，那么 hexo g 只会重新生成改动的文件。使用该参数的效果接近 hexo clean && hexo generate

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
##### 新建一篇文章
```
$ hexo new <title>
```
此处新建的时候。title 最好是英文的标题，如果_config.yml配置的路由规则（即permalink）是默认值 :year/:month/:day/:title/ ，那么进入文章后路由就有中文了，有中文使用 gitment 评价系统时，登陆 github 账号会出现 404 问题。

##### 发布博客

运行下面的命令
清除缓存文件 (db.json) 和已生成的静态文件 (public),并将 public中的博客静态文件推送到地址B上。

```
$ hexo clean && hexo deploy
```
过一会去 地址B 中的setting 的 GitHub Pages 的网址上去查看发布的博客即可。

#### 五、切换主题

[hexo主题](https://hexo.io/themes/)网站上面有很多的主题可以选择，找到选中主题的github地址，切换主题具体操作需要看每个主题的ReadMe即可。如果自己要修改东西也可以进入themes目录下去调整相关代码。

#### 六、引入husky
由于博客的静态文章和项目的代码是分开两个仓库存放的，所以每次发布文章后，还需要把代码推到仓库中，引入husky就是利用git hooks配置预处理脚本。在项目commit后，直接发布文章，也就是把博客静态文件推送到远程仓库。
 
 1. 安装husky
 ```
 $ npm install --save-dev husky
 ```
 2. package.json中，与“scripts“并列，添加如下内容：
 ```
"husky": {
    "hooks": {
        "pre-commit":"npm run clean && npm run deploy"
    }
},
 ```

<!-- #### 六、引入nodemon
nodemon用来监视node.js应用程序中的任何更改并自动重启服务,非常适合用在开发环境中。nodemon将监视启动目录中的文件，如果有任何文件更改，nodemon将自动重新启动node应用程序。
1. 安装nodemon
```
$ npm install --save-dev nodemon
```
2. nodemon配置saf  -->

#### 六、引入gitment评论
gitment是基于github的issue来显示评论的一个插件，[Gitment评论功能接入踩坑教程](https://www.jianshu.com/p/57afa4844aaa)
1. 注册 OAuth Application 进入 https://github.com/settings/applications/new 注册一个application
2. 注册成功后就可以拿到 Client ID 和 Client Secret
3. 在根目录中找到 _config.yml文件，修改comment如下,
```
comment:
  gitment:
    enable: true // 开启gitment
    owner: xiaopf // github用户名
    repo: xiaopf.github.io // 评论存放的仓库，我直接用的博客静态资源存放的仓库了
    client_id: xxxxx // 上文获得的Client ID
    client_secret: xxxx // 上文获得的Client Secret
```
 