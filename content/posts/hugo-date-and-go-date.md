---
title: "Hugo的time.Format或者说时间的格式"
date: 2020-11-26T01:02:43-05:00
draft: false
summary: "其实是Go的"
tags: [hugo, Go]
---

在修github-style[这个issue](https://github.com/MeiK2333/github-style/issues/25)的时候接触到了hugo的时间设置，感觉必须要记录一下这个time.Format的事情。

首先Hugo是用Go写的，而在Go里面并没有用之前常见的`“YYYY MM DD”`的方式来表示年月日的时间，而是用了`“2006-01-02”`这样一个固定的参考时间(reference time)，而Hugo也是沿用了Go的这个设计。为什么要这样呢？相比之下可读性更强。而index的问题则是通过固定的reference time来解决的。对于这个特殊的参考时间`Mon Jan 2 15:04:05 -0700 MST 2006`，可以从下表看出1到7的index对应。

```
Jan        -> 1      -> Month
2          -> 2      -> Day-of-Month
15 = 3PM   -> 15/3   -> hour
04         -> 4      -> minute
05         -> 5      -> second
2006       -> 6      -> year
-0700      -> 7      -> time-zone
```

另外一件事也是时间相关的，在Hugo里面使用Date, LastDate, PublishDate的时候会在对应时间没有设置的时候用已有的时间进行替换。比如说最近修改时间LastMod就会根据条件被替换成Date或者.GitInfo
```
.Lastmod
the date the content was last modified. .Lastmod pulls from the lastmod field in a content’s front matter.
1. If lastmod is not set, and .GitInfo feature is disabled, the front matter date field will be used.
2. If lastmod is not set, and .GitInfo feature is enabled, .GitInfo.AuthorDate will be used instead.
```
这也意味着时间类的参数很少会有拿不到值的情况。那要检查用户有没有设置lastmod就不能直接`{{if .LastMod}}`了，而是需要比如`{{ if ne .Lastmod .Date }}`. 可以参考这个[帖子](https://discourse.gohugo.io/t/method-to-automate-last-modified-date/970/13).