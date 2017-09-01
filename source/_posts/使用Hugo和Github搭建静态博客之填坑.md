---
date: 2016-12-14T10:57:00+08:00
description: ""
tags:
- Hugo
- Github
- Pages
- Blog
title: 使用Hugo和Github搭建静态博客之填坑
categories: 技术分享
photos: /images/gan_00.png
---

> 我的静态博客已经搭建成功了，并且使用Markdown记录了过程，但期间踩了一路的坑，今天总结一下其中一个：Hugo成功部署到Github Pages，但访问博客时不加载网页布局和样式。

这算是遇到最大的坑了，前后折腾了半个月的时间，直到找到了组织（Hugo交流QQ群：512499080），群主大大[@coderzh](http://www.coderzh.com/) 一语道破天机。
![](/images/hugo_tk_01.png)

<!--more-->

#### 1 先来回顾一下如何踩上这个大坑的

##### 1.1 使用`hugo new site` 创建一个新站点时，配置文件`config.toml` 中的 `baseurl` 默认是 `http://`

	baseurl = "http://replace-this-with-your-hugo-site.com/

clone spf13.com的博客项目中`baseurl` 也是 `http://`

	baseurl = "http://spf13.com/"

在之后修改时，直接把博客地址改为了我自己的

	baseurl = "http://imkind.github.io/"

##### 1.2 自2016年6月15日起，使用Github Pages默认域名搭建个人站点，已被强制要求HTTPS访问，即使用HTTP访问，也会被重定向到HTTPS。参见《Securing your GitHub Pages site with HTTPS》 https://help.github.com/articles/securing-your-github-pages-site-with-https/



> You can enforce HTTPS to add a layer of encryption for traffic to your GitHub Pages site if it has a github.io domain.

> With HTTPS enforcement enabled, HTTP requests to your GitHub Pages site will be transparently redirected to HTTPS.

> HTTPS enforcement is required for GitHub Pages sites created after June 15, 2016 and using a github.io domain. If you created your GitHub Pages site before June 15, 2016, you can manually enable HTTPS enforcement. HTTPS is not supported for GitHub Pages using custom domains.

这一点可以在repository的settings中得到证实

> √ Enforce HTTPS — Required for your site because you are using the default domain (imkind.github.io)

> HTTPS provides a layer of encryption that prevents others from snooping on or tampering with traffic to your site.

> When HTTPS is enforced, your site will only be served over HTTPS. Learn more.

此时，Hugo中配置的HTTP与Github Pages强制使用的HTTPS不一致，导致站点图片、css和js文件无法加载，访问时造成上图的情况

> If you enable HTTPS for your site, and your site's HTML still references images, CSS, or JavaScript over HTTP, then your site is serving mixed content, and you may have trouble loading assets. Serving mixed content also makes your site less secure.

>To remove your site's mixed content, improve your site's security, and resolve problems related to loading mixed content, edit your site's HTML files and change http:// to https:// so that all of your assets are served over HTTPS.

#### 2. 填坑，更多解决方案参见《Resolving problems with mixed content》https://help.github.com/articles/securing-your-github-pages-site-with-https/

##### 2.1 综上，在配置文件config.toml中把`http`修改为`https`

	baseurl = "http://imkind.github.io/"

修改为

	baseurl = "https://imkind.github.io/"

然后，重新运行 `hugo` 生成静态站点，分别把改动push到IMkind_blog_hugo和imkind.github.io

##### 2.2 但是，我发现[spf13.com](http://spf13.com/)、[blog.coderzh.com](http://blog.coderzh.com/)、[nanshu.wang](http://nanshu.wang/)、[blog.bpcoder.com](http://blog.bpcoder.com/)等都能使用HTTP正常访问，这可能是因为绑定了自己的域名。

《Securing your GitHub Pages site with HTTPS》 https://help.github.com/articles/securing-your-github-pages-site-with-https/ ，第三段最后一句：

> HTTPS is not supported for GitHub Pages using custom domains.

#### *彩蛋*
附上群主大大[@coderzh](http://www.coderzh.com/)如何一语道破天机
![](/images/hugo_tk_02.png)
