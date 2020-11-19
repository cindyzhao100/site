---
title: "给博客设置Google Analytics"
date: 2020-11-19T16:34:10-05:00
draft: false
tags: [google analytics]
---
"享受在后台看博客阅览量为0的快乐…大概!"
<!--more-->
## 如何设置

原以为Hugo在config.toml里面内置了googleAnalytics的变量，然后也提供了internel template,设置谷歌分析根本不成问题。结果翻车了，半天统计不上数据。

搜索出来的结果是，谷歌的分析更新啦。所以拿到的不是UA开头的tracking id，而是G开头的measurement id。中文页面翻译是追踪id和衡量id。而衡量id需要用的是Google tagmanager而不是原先的google analysis的代码片段放在header里。不过对原先UA的支持还是有的，需要在创建properly的时候选择高级选项，然后把universal analysis勾上。

用衡量id可以拿到更多的数据，但如果只想看访问量可以只用原先的追踪id。如果要用新的衡量id，则可以在原先的代码head.html里添加上
```
<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-EGLPCN8V7K"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'G-xxxxxx');
</script>
```
注意里面G-xxxxx需要替换。也可以在config.toml的param下面设置gid = "G-xxxxxx"，然后在代码里用{{.Site.Params.gid}}来取值。

还有一个问题，自己在测试的时候的访问也会被算进去。参考[这里](https://github.com/wowchemy/wowchemy-hugo-modules/blob/b028457922e1c596c3c1bf993e6a28a6b645b127/layouts/partials/marketing/google_analytics.html#L4)的做法用环境变量来区分生产环境和开发环境。需要对使用的主题进行更改。
```
{{ if eq (getenv "HUGO_ENV") "production"}}
代码
{{ end }}
```

然后在deploy.sh里面用env HUGO_ENV="production" hugo来创建网站。