> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/368407566)

**什么是博客生成器?**
-------------

这里的博客生成器也叫静态网站生成器，是一种将文本文档通过一些处理生成一个有机整体的 html 语言的网站，由于生成的静态网页可以直接托管在主机或 vps 上，配合 nginx 或 Apache 就可以供网民使用。github 与 gitcafe 等网站也支持免费托管静态网页文件，非常方便。

所使用的文本文档可以是 markdown 也可以是 textfile，可供有相应需求的人士使用。

劣势在于每次更新网页必须重新生成整个网站，用时通常是几秒到几分钟，随网站规模而变，略麻烦！如果需要解决这些劣势问题，可以选择用 WordPress 搭建个人博客。[宝塔面板搭建 WordPress 完整教程](https://link.zhihu.com/?target=https%3A//www.yundashi168.com/42.html) 因为 WordPress 带有 web 可视化的管理后台，自动可定制的搭建千变万化的各种网站。

众多生成器分别用不同的语言写出，也有各自特点，例如 Jekyll、Hexo、Hugo、Pelican 等。本文就热门的博客生成器进行一定比较，供读者参考。

**社区支持**
--------

jekyll、hexo、hugo 部署时通常需要敲入很多代码，一行一行的命令下去很难讲会不会遇到什么问题。一旦遇到问题，我们通常需要求助于网络。当社区支持越强时，也就是说有更多的人在与你用相同的工具，遇到与你一样问题的人应当有很多，此时求得答案也越容易。

量化社区支持的方法，此处西蒙宫用 github 上的 star 的数目来量化，当然也可以有其他的方法。

**截止 2021 年 4 月 24 日，**

☆star 数目：

*   Hugo ：51.5k [Github 地址](https://link.zhihu.com/?target=https%3A//github.com/gohugoio/hugo) / [官网地址](https://link.zhihu.com/?target=https%3A//gohugo.io/) / [主题汇总 1](https://link.zhihu.com/?target=https%3A//themes.gohugo.io/)/ [主题汇总 2](https://link.zhihu.com/?target=https%3A//www.gohugo.org/theme/)/ [主题汇总 3](https://link.zhihu.com/?target=https%3A//hugothemesfree.com/)
*   Jekyll：42.6k [Github 地址](https://link.zhihu.com/?target=https%3A//github.com/jekyll/jekyll) / [官网地址](https://link.zhihu.com/?target=https%3A//jekyllrb.com/) / [主题汇总 1](https://link.zhihu.com/?target=http%3A//jekyllthemes.org/)/ [主题汇总 2](https://link.zhihu.com/?target=https%3A//jekyllthemes.io/free)/ [主题汇总 3](https://link.zhihu.com/?target=http%3A//themes.jekyllrc.org/)
*   Hexo： 32.6k [Github 地址](https://link.zhihu.com/?target=https%3A//github.com/hexojs/hexo) / [官网地址](https://link.zhihu.com/?target=https%3A//hexo.io/) / [主题汇总](https://link.zhihu.com/?target=https%3A//hexo.io/themes/)

**编译速度**
--------

根据[这里](https://link.zhihu.com/?target=http%3A//conscientiousprogrammer.com/blog/2015/05/31/why-i-switched-from-octopress-2-to-hugo/)提供的测试结果

生成 585 篇文章，需要的时间（单位：秒）为：

*   Jekyll: 15.90
*   Hugo: 4.90
*   Hexo 的数据应当介于二者之间。

之所以有这样的结果是**因为 Hugo 使用 go 语言是一种编译型语言，速度非常快**，而 Jekyll 使用 ruby 编写，hexo 使用 nodejs 编写，ruby 与 nodejs 都是面向对象的高级解释型语言，执行效率比编译型语言要低。为什么 Hexo 介于二者之间，因为 Nodejs 的 V8 引擎比 Ruby 实现的方法要快。而且网上也有相关测评，具体可以参考众多 Hexo 生成的博客网站，例如[这里](https://link.zhihu.com/?target=http%3A//chenall.net/post/jekyll_or_hexo/)

**其他方面**
--------

*   Jekyll 有 github 支持，可以将 markdown 文件直接放到 git 仓库，github 会自动生成网页文件。（Github 一直是一个亲 ruby 的社区）
*   Hexo 提供了方便的部署命令，可以做到一条命令部署到 github 上。
*   Hugo 的官方文档写的非常好，部署简洁。前两者部署时需要安装很多依赖，而 hugo 可以直接提供二进制文件运行，甚至不需要 root 权限。

原文更精彩：[https://www.yundashi168.com/254.html](https://link.zhihu.com/?target=https%3A//www.yundashi168.com/254.html)