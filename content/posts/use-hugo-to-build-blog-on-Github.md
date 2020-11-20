---
title: "用Hugo在GitHub上搭建个人博客"
date: 2020-11-18T11:23:00-05:00
draft: false
tags: [hugo]
summary: "虽说更重要的是能坚持写文章，但白嫖能增加幸福感啊!" 
---
## 起因
某日在翻Github的时候偶然看到了[奇趣保罗](https://paul.ren/)的博客，视觉效果非常棒。于是起了自己也做一个的心思。可是Typocho和WordPress的注册域名和备案一系列的操作感觉很繁琐，并且没法白嫖(重点)。而MkDocs虽然免费，但作为专门写文档的工具还是缺了点博客需要的部件。

再一通谷歌找到了[Meik2333](https://github.com/MeiK2333)的github-style Hugo博客主题，也是现在这个页面使用的主题。上手之后发现Hugo生成页面的速度真的非常快（应该说不愧是Go语言的产物），而且文档上面也有deploy到GitHub Page上的方法。用markdown写博客又和之前用MkDocs的想法异曲同工。就这个了！

## 准备工作
首先安装Hugo,在mac上可以直接用brew
```
brew install hugo
```
随后用hugo生成基础的网站框架,这里blogs可以替换成自己想要的文件夹名字
```
hugo new site blogs
```
进去blogs，待会要修改里面的congig.toml文件以及往content(内容)和theme(风格)里添加文件。总之先设置下主题，这里用的是github-style。这行指令会下载最新版本的github-style到theme/github-style的位置
```
git submodule add git@github.com:MeiK2333/github-style.git themes/github-style
```
接下来修改config.toml文件。这个是用来设置Hugo的。github-style给了一个基本模板
```
baseURL = "https://example.com/"
title = "GitHub Style"
googleAnalytics = "UA-123-456-789"
theme = "github-style"
copyright = "© 2019. Theme by <a href=\"https://github.com/MeiK2333/github-style\"><span>github-style</span></a>"

[params]
  author = "example"
  description = "example"
  github = "example"
  facebook = "example"
  twitter = "example"
  misskey = "user profile url"
  mastodon = "user profile url"
  email = "example@domain.com"
  utterances = "example/example.github.io"
  avatar = "https://example.com/images/avatar.png"
  url = "https://example.com"
  keywords = "blog, google analytics"
```
baseURL是我们想要的github.io地址，这里我的用户名是liz2020,而且我想把blogs设置在/blogs这个route下，所以设置的是liz2020.github.io/blogs。GoogleAnalytics是用来分析网站访问人数之类的数据的，可以暂时不改动，不会影响运行。params里面的参数对应的是左边中部显示的图标和链接，没有的可以直接删掉那行。比如说我不想放脸书地址，就可以直接把facebook那行删掉。

avatar是头像，这里可以直接用自己GitHub的用户头像。去GitHub页面然后右键inspect自己的头像，找到src里面的图片地址复制过来就可以。

utterances则是文章的评论系统，是将github issues当做放评论的地方，让任何有GitHub账号的人都可以回复。这里把example换成自己的用户名，我的是liz2020/liz2020.github.io。

## 创建文章
先创建about(关于)页面
```
hugo new about.md
```
这会在content里面创建about.md，我们打开那个文件修改一下。需要吧layout设置成“about”,然后draft那行可以删除
```
---
title: "About"
date: 2020-11-18T10:33:32-05:00
layout: "about"
---
```
其实到现在这个阶段已经可以运行网站来查看情况了。直接运行hugo server，他会显示服务放在http://localhost:1313/blogs/。可以打开阅览器查看
```
hugo server
```
终于到创建博客文章的时候了，这个其实和刚刚创建about页面差不多，不过文章得放在content/posts里面
```
hugo new posts/article_name.md
```
这会创建一个叫article_name.md的文章。里面的默认内容有生成时间，title（标题），还有“draft：true”。draft是草稿，只有把这个改成false文章才会显示。

## 发布到GitHub
这个最好参考[官方文档](https://gohugo.io/hosting-and-deployment/hosting-on-github/)，我选的是里面GitHub User or Organization Pages这个部分的做法。里面会提供完整的脚本，可以直接复制使用。唯一注意的是里面有个upstream需要对应修改，得看一下git remote里面自己的remote叫什么名字，我的是origin。这里贴一下我用的脚本好了：
```
#!/bin/sh

if [ "`git status -s`" ]
then
    echo "The working directory is dirty. Please commit any pending changes."
    exit 1;
fi

echo "Deleting old publication"
rm -rf public
mkdir public
git worktree prune
rm -rf .git/worktrees/public/

echo "Checking out gh-pages branch into public"
git worktree add -B gh-pages public origin/gh-pages

echo "Removing existing files"
rm -rf public/*

echo "Generating site"
hugo -t github-style

echo "Updating gh-pages branch"
cd public && git add --all && git commit -m "Publishing to gh-pages (publish.sh)"

#echo "Pushing to github"
#git push --all
```
使用办法是
```
sh deploy.sh
```
不过用了之后你大概会发现GitHub上没变化，因为脚本最后一行的git push --all是被注释掉的，所以我们的改变还没推到GitHub上。文档里面说这是让我们可以发布前自己决定要不要检查一下。感觉没问题就
```
git push --all
```
别忘了去GitHub自己的repo里面把GitHub Pages打开，然后使用gh-pages这个branch。

## 题外话
其实github-style这个主题不是特别理想
- 缺少了搜索功能
- 默认是打开夜间模式，没有地方切换
- 文章点开后没有上一篇下一篇的按钮，得退回去再点
- 和GitHub页面过于像，一些案件会造成困扰。比如Pinned其实是最新发布文章，上下的GitHub图标是回主界面，右上角的头像没有什么用

所以有空的话我之后会在这个主题的基础上修改一下。不过真的很感谢原作者Meik2333对主题的开源