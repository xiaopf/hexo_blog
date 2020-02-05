---
title: git 按目录配置多个用户
catalog: true
header-img: defaultHeaderBg.jpg
date: 2020-02-05 10:21:08
subtitle: 使用git includeIf 特性配置不同目录的用户
tags:
- git
catagories:
- git
---

## git 按目录配置多个用户
由于在日常工作中，公司用的是gitlab，用户名和邮箱一套，我自己个人用的是github，用户名和邮箱是另一套，所以在电脑中我需要配置两个git用户，根据这个需求找到了git在2.13.0版本中包含了一个新的功能includeIf配置，可以把匹配的路径使用对应的配置用户名和邮箱。下面就一步一步的来分解如何配置多用户。
### 一 配置 SSH KEY
#### 1.生成ssh key
```
// 进入 .ssh 目录
cd ~/.ssh
// 如果没有该目录，需要自己生成
mkdir ~/.ssh
```
后续生成的秘钥需要存入这个目录中的

```
// 使用邮箱生成 SSH KEY（我先用公司邮箱的生成）
ssh-keygen -t rsa -C "gitlab@email.com" 
```
上面的命令执行后命令行中会出现下面的结果，这里如果你什么都不输入会使用括号中的id_rsa 这个名字，产生两个文件：一个id_rsa，一个id_rsa.pub
```
Enter file in which to save the key (/Users/xxx/.ssh/id_rsa):
```
因为我们需要两个用户，所以需要在这输入key，以区分不同用户产生的文件（可以用id_rsa_github和id_rsa_gitlab，能区分开就好）。后续密码就可以不设置了，一路回车直到出现下面的样子即可。

```
xxx@bogon ~ % ssh-keygen -t rsa -C "mywork@email.com" 
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/xxx/.ssh/id_rsa): xxxx
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in xxxx.
Your public key has been saved in xxxx.pub.
The key fingerprint is:
SHA256:KNkUWr07DhJHsuIE0c4EVLeJbOrVh5aprXBQEA9grBw mywork@email.com
The key's randomart image is:
+---[RSA 2048]----+
|BXo. .o.         |
|oEB +o+..        |
|o=.*.*.  .       |
|..O ++=..        |
| = ooOo.S.       |
|. + =.o o        |
| o o o o .       |
|  o .   .        |
|   .             |
+----[SHA256]-----+
```
上面的步骤因为有两个用户，所以需要执行两次，产生两个KEY。
#### 2. 配置SSH KEY
下面我们就需要把 id_rsa.pub（这里根据上面起的名字决定）文件内容复制到github和gitlab中了，大概步骤如下，不再赘述：
- 登录github，然后点击头像进入Settings，找到SSH and GPG keys，点击 New SSH key，title随便写，能区分出设备即可，key 里面记得带上ssh-rsa字段。
- 登录gitlab，然后点击头像进入Settings，点击SSH Keys，key 里面记得带上ssh-rsa字段，title随便写，能区分出设备即可。

#### 3. 配置 .ssh目录config文件
进入.ssh目录，编辑config文件
```
cd ~/.ssh
vim config
//在config文件中添加下面内容
Host git.xxx.com
   User xxxx
   Hostname git.xxx.com
   IdentityFile ~/.ssh/id_rsa_gitlab

Host github.com
   User xxx
   Hostname github.com
   IdentityFile ~/.ssh/id_rsa_github
```
配置完成后，测试一下是否配置成功
```
ssh -T git@github.com 
Hi xxxx! You've successfully authenticated, but GitHub does not provide shell access.

ssh -T git@git.xxx.com 
Welcome to GitLab, @xxx!
```
### 二 使用includeIf实现按目录配置不同用户

在~/目录下面生成三个配置文件，
- .gitconfig // 全局通用配置文件
- .gitconfig-gitlab // 公司项目配置文件（名字随意起，主要作区分用，但是需要和.gitconfig中对应起来）
- .gitconfig-github // 个人项目配置文件（名字随意起，主要作区分用，但是需要和.gitconfig中对应起来）

全局通用配置文件~/.gitconfig里面的内容是：主要是通过includeIf配置匹配不用的目录映射到不同配置文件上，
```
[includeIf "gitdir:~/gitlab/"]
    path = .gitconfig-gitlab
[includeIf "gitdir:~/github/"]
    path = .gitconfig-github
```
上面的 gitdir:~/gitlab/ 和 gitdir:~/github/ 主要是匹配不同目录，/gitlab/和/github/是不同的目录，公司目录和个人目录，名字也是随意的，但是需要和系统中的具体目录名对应上。

个人工程配置文件~/.gitconfig-gitlab
```
[user]
    name = gitlab-username
    email = gitlab-email@email.com
```
公司工程配置文件~/.gitconfig-github
```
[user]
    name = github-username
    email = github-email@email.com
```

注意事项：
- 文件~/.gitconfig里面的includeIf后面的path最后需要/结尾
- 文件~/.gitconfig里面原有的user部分需要删除
- 个人工程目录和公司工程目录需要要求是非包含关系，就是这两个工程目录配置路径不可以是父子关系。

最后配置完成可以去具体的尝试一下拉代码，推代码，github和gitlab上面的名字是否是正确的了，配置两个github或两个gitlab账户，原理上来说是相同的。