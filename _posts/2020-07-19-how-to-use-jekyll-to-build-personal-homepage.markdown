---
layout: post
title: 创建这个网站的教程
---

这个网站是用[Jekyll](https://jekyllrb.com/)和[模板](http://jekyllthemes.org/themes/type/)进行创建的，最后通过github发布。

1. 具体教程直接参考Jekyllrb官网就好。注意使用bundle进行本地管理，以及需要先删除模板中的Gemfile.lock，再用bundle install进行配置，执行bundle exec jekyll serve就可以本地访问网页了。


2. 通过github pages发布：
	注意将模板里的CNAME中的内容换成我们需要发布的网页（我的是https://xinguozju.github.io　），或者其他已经购买的域名。
	github新建一个repo，仓库名字为xxx.github.io，其中xxx为你github的username，上传代码之后，https://xxx.github.io　就是创建好的网站了。