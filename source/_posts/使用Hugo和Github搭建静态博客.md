---
title: 使用Hugo和Github搭建静态博客
date: 2016-12-02T09:53:13+08:00
description: ""
tags:
- imkind
- hugo
- Github Pages
- blog
categories: 技术分享
photos: /images/photos/hugo_00.png
---

> 我的静态博客使用Hugo生成，部署在Github Pages个人主页上，为了完成搭建，期间学习了Hugo、Git、Markdown。由于接触静态博客不久，对很多东西一知半解，本文仅记录了在Windows 10上基于Hugo作者[Steve Francia](https://stevefrancia.com/)的个人博客[http://spf13.com/](http://spf13.com/)的源代码定制自己博客的过程，绝不权威，仅供参考。

<!--more-->

## 1.准备
### 1.1 [注册Github](https://github.com/)

### 1.2 [安装并配置Git for Windows](https://git-scm.com/downloads)
默认选项安装到Ajusting your PATH environment时，勾选第二项Run Git from Windows Command Prompt，即可在windows命令行使用git命令，不过Git Bash用户体验更好。

Git全局配置

	git config --global user.name [your-name]
	git config --global user.email [your-email-address]
	git config --global color.ui true

配置SSH Key

	ssh-keygen -t rsa -C "youremail@example.com"

一路回车使用默认值，完成之后在用户主目录中进入 `.ssh`目录，可以看到

	id_rsa		私钥，不能泄露出去
	id_rsa.pub	公钥，可告诉任何人

登录Github，依次点击"settings"——>"SSH and GPG keys"——>"New SSH Key"
![](/images/20161202/git_13_01.png)

填写Title，在Key文本框里粘贴`id_rsa.pub`文件的内容，点击"Add SSH Key"

### 1.3 [安装Hugo](https://github.com/spf13/hugo/releases)
#### 1.3.2 Ubuntu

	sudo apt-get install hugo

#### 1.3.1 Windows 10
1. 下载hugo-0.17-Windows-64bit.zip，并解压缩到C:\Program Files\Hugo\
2. 将hugo-0.17-windows-amd64.exe修改为hugo.exe
3. 运行sysdm.cpl，添加环境变量：高级-环境变量-系统变量-编辑PATH-新建-‪C:\Program Files\Hugo

## 2. 定制Hugo
### 2.1 Clone大神spf13的博客项目源代码
进入工作目录

	$ cd d/HelloWorld/hugo/
	$ pwd
	/d/HelloWorld/hugo

clone大神spf13的博客项目源代码：[spf13.com](https://github.com/spf13/spf13.com)

	$ git clone https://github.com/spf13/spf13.com.git
	Cloning into 'spf13.com'...
	remote: Counting objects: 894, done.
	remote: Total 894 (delta 0), reused 0 (delta 0), pack-reused 894
	Receiving objects: 100% (894/894), 17.35 MiB | 20.00 KiB/s, done.
	Resolving deltas: 100% (346/346), done.
	Checking out files: 100% (366/366), done.
	$ ls -A spf13.com/
	.git/  .gitignore  archetypes/  config.toml  content/  layouts/  README.md  static/


重新打开一个git bash，运行站点

	$ cd /d/HelloWorld/hugo/spf13.com/
	$ hugo server -w
	Started building site
	0 of 12 drafts rendered
	0 future content
	151 pages created
	0 non-page files copied
	0 paginator pages created
	273 tags created
	22 topics created
	in 718 ms
	Watching for changes in D:\HelloWorld\hugo\spf13.com\{content,layouts,static}
	Serving pages from memory
	Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
	Press Ctrl+C to stop

在浏览器输入localhost:1313,查看效果:

![](/images/20161202/hugo_2_1_01.png)

### 2.2 删除多余文件，修改项目名称

删除spf13.com\content\下三个目录post、presentation、project,这是导航栏对应的博文存放目录，删除后，稍后将创建自己的分类博文目录

	$ rm -r spf13.com/content/post/
	$ rm -r spf13.com/content/presentation/
	$ rm -r spf13.com/content/project/

删除spf13.com\static\media\下的所有图片文件(spf13博文中的图片)

	 rm spf13.com/static/images/*

Ctrl+C停止站点运行，修改目录名为自己的项目名称

	$ mv spf13.com/ IMkind_blog_hugo

删除项目的Git版本库

	$ rm -rf IMkind_blog_hugo/ .git/
	$ rm IMkind_blog_hugo/ .gitignore

	$ cd IMkind_blog_hugo/
	$ git status
	fatal: Not a git repository (or any of the parent directories): .git

创建新的版本库

	$ git init
	Initialized empty Git repository in D:/HelloWorld/hugo/IMkind_blog_hugo/.git/

### 2.3 修改配置文件config.toml
![](/images/20161202/hugo_2_3_01.png)

	baseurl = "https://imkind.github.io/"
	title = "Continuous  Delivery"
	languageCode = "en-us"
	disqusShortname = ""
	copyright = ""
	MetaDataFormat = "yaml"

	[author]
	    name = "IMkind"

	[indexes]
	    tag = "tags"
	    topic = "topics"

* baeurl 博客地址，必须使用`https://`,切记！
* title	博客标题
* disqusShortname  Disqus的用户名，不使用评论功能可留空
* copyright	版权信息
* name	博客作者署名
* 其它项默认参数即可

启动站点，在浏览器可以看到效果了：

![](/images/20161202/hugo_2_3_02.png)


### 2.4 修改博客副标题
![](/images/20161202/hugo_2_4_01.png)

在 layouts\partials\subheader.html

	<div class="icon-spf13-3" id="logo">

	<div id="byline">by Steve Francia</div>

修改为

	<div class="icon-happy" id="logo">

	<div id="byline">Hello , IMkind</div>

**spf13.com博客中所有的图标都是以 `css类` 来实现，`\static\static\css` 中以 `.icon-` 开头的类各自定义了一个图标。**

例如：`.icon-happy` 即 笑脸图标，浏览器查看效果：

![](/images/20161202/hugo_2_4_02.png)

### 2.5 修改导航条
![](/images/20161202/hugo_2_5_01.png)

在 layouts\partials\nav.html

	<ul id="mainnav">
		<li>
		    <a href="/post/">
		    <span class="icon"> <i aria-hidden="true" class="icon-quill"></i></span>
		    <span> blog </span>
		</a>
		</li>
		<li>
		<a href="/project/">
		    <span class="icon"> <i aria-hidden="true" class="icon-console"></i></span>
		    <span> code </span>
		</a>
		</li>
		<li>
		<a href="/presentation/">
		    <span class="icon"> <i aria-hidden="true" class="icon-stats"></i></span>
		    <span> talks </span>
		</a>
		</li>
		<li>
		<a href="http://stevefrancia.com">
		    <span class="icon"> <i aria-hidden="true" class="icon-13"></i></span>
		    <span> me </span>
		</a>
		</li>
	</ul>

修改为

	<ul id="mainnav">
		<li>
		    <a href="/感/">
		    <span class="icon"> <i aria-hidden="true" class="icon-quill"></i></span>
		    <span> 感 </span>
		</a>
		</li>
		<li>
		<a href="/猿/">
		    <span class="icon"> <i aria-hidden="true" class="icon-code"></i></span>
		    <span> 猿 </span>
		</a>
		</li>
		<li>
		<a href="/狮/">
		    <span class="icon"> <i aria-hidden="true" class="icon-console"></i></span>
		    <span> 狮 </span>
		</a>
		</li>
		<li>
		<a href="/about/index.html">
		    <span class="icon"> <i aria-hidden="true" class="icon-user"></i></span>
		    <span> 吾 </span>
		</a>
		</li>
	</ul>

注意：在源码中修改了每个导航标题之后，还要到www\content下创建对应的目录。但是直接创建的目录并不能起作用，即使目录中存在Markdown格式的文档，点击之后也会是一片空白。

到底该怎么创建对应的目录呢？使用hugo new 命令：

	$ hugo new about.md
	D:\HelloWorld\hugo\IMkind_blog_hugo\content\about.md created
	$ hugo new 感/我的第一篇感.md
	D:\HelloWorld\hugo\IMkind_blog_hugo\content\感\我的第一篇感.md created
	$ hugo new 猿/我的第一篇猿.md
	D:\HelloWorld\hugo\IMkind_blog_hugo\content\猿\我的第一篇猿.md created
	$ hugo new 狮/我的第一篇狮.md
	D:\HelloWorld\hugo\IMkind_blog_hugo\content\狮\我的第一篇狮.md created



这样就创建好了三个对应目录感、猿、狮，并同时创建了markdown格式的博文，往每篇博文中写入一段内容，即可在浏览器看到效果，在导航栏点击对应的标题，显示对应目录下所有的博文标题。

注意：必须往每篇博文正文写入一段内容，否则在页面上点击对应的导航标题都会跳转到空白页面。

![](/images/20161202/hugo_2_5_02.png)

导航栏每行默认显示四个标题，超过会另取一行。可随意增加`<li>...</li>`数量，并创建对应的目录,如下：

![](/images/20161202/hugo_2_5_03.png)

### 2.6 修改侧边栏分享和社交
![](/images/20161202/hugo_2_6_01.png)

在 layouts\partials\social.html 按需增减、修改`<li>...</li>`

    <ul id="social">
        <li id="follow">
            <span class="icon icon-heart-2"> </span>
            <span class="title"> follow </span>
            <div class="dropdown follow">
                <ul class="social">
                    <li> <a href="http://github.com/imkind" target="_blank" title="GitHub" class="github"><span class="icon icon-github"></span>GitHub</a> </li>
                    <li> <a href="http://weibo.com/kind0214" target="_blank" title="Weibo" class="github"><span class="icon icon-feed"></span>Weibo</a> </li>
                    <li> <a href="http://www.facebook.com/kind0214" target="_blank" title="Join me on Facebook" class="facebook"><span class="icon icon-facebook"></span>Facebook</a> </li>
                    <li> <a href="http://www.twitter.com/kind0214" target="_blank" title="Follow me on Twitter" class="twitter"><span class="icon icon-twitter"></span>Twitter</a> </li>
                    <li> <a href="http://www.linkedin.com/in/luminggui" target="_blank" title="LinkedIn" class="linkedin"><span class="icon icon-linkedin"></span>LinkedIn</a> </li>
                    <li> <a href="mailto:luminggui0214@gmail.com" target="_blank" title="Send an email" class="email"><span class="icon icon-mail"></span>Email</a> </li>
                </ul>
                <span class="subcount icon-arrow-right"></span>
            </div>
        </li>
    </ul>

在 static\css\style.css 调整侧边栏位置

	#social{position:absolute;bottom:2em;width:90%;left:5%}

修改为

	#social{position:relative;top:1em;width:90%;left:5%}

浏览器查看效果

![](/images/20161202/hugo_2_6_02.png)

### 2.7 修改脚注

![](/images/20161202/hugo_2_7_01.png)

在 layouts\partials\footer.html 修改脚注内容

			<footer>
			  <div>
			    <p>
			    &copy;2016
			        <span itemprop="name">IMkind .</span>
			    Powered by <a href="http://gohugo.io">Hugo</a>.
			        Theme by <a href="http://servergrove.com">Server Francia</a>.
			    </p>
			  </div>
			</footer>
		</body>
	</html>

在浏览器查看效果

![](/images/20161202/hugo_2_7_02.png)

### 2.8 修改博文正文显示页的布局

![](/images/20161202/hugo_2_8_01.png)

#### 2.8.1 修改博客作者相关介绍

![](/images/20161202/hugo_2_8_1_01.png)

在 layouts\partials\details.html  对作者介绍部分 `<div><section id="author">...</section></div>` 稍作修改即可，由于本人实在太过平淡无奇，索性去掉这一部分

	<!--
	<div><section id="author">
	...
	</section></div>
	-->

#### 2.8.2 修改博文创建时间、字数统计、阅读时间、tags、topics的显示位置

![](/images/20161202/hugo_2_8_2_01.png)

将博文创建时间、字数统计、阅读时间、tags、topics的显示位置调整到标题和正文之间

在 layouts\_default\single.html

	<section id="main">
	  <h1 itemprop="name" id="title">{{ .Title }}</h1>
	  <div>
	        <article itemprop="articleBody" id="content">
	           {{ .Content }}
	        </article>
	  </div>
	</section>

	{{ partial "meta_aside.html" . }}


修改为

	<section id="main">
	  <h1 itemprop="name" id="title">{{ .Title }}</h1>
	  {{ partial "meta_aside.html" . }}
	  <div>
	        <article itemprop="articleBody" id="content">
	           {{ .Content }}
	        </article>
	  </div>
	</section>

在浏览器查看效果

![](/images/20161202/hugo_2_8_2_02.png)

#### 2.8.3 修改上下篇链接的显示位置

![](/images/20161202/hugo_2_8_3_01.png)

在 layouts\partials\details.html 中删除或注释掉上下篇链接相关代码

	<!--
    <div>
        <section id="prev">
            &nbsp;{{if .Prev}}<a class="previous" href="{{.Prev.Permalink}}"><i class="icon-arrow-left"></i> {{.Prev.Title}}</a><br>{{end}}
        </section>
		<section id="next">
            &nbsp;{{if .Next}}<a class="next" href="{{.Next.Permalink}}">{{.Next.Title}} <i class="icon-arrow-right"></i></a>{{end}}
        </section>
    </div>
	-->

新建 layouts\partials\prevnext.html，代码如下

    <div>
        <section id="prev">
            &nbsp;{{if .Prev}}<a class="previous" href="{{.Prev.Permalink}}"><i class="icon-arrow-left"></i> {{.Prev.Title}}</a><br>{{end}}
        </section>
        <section id="next">
            &nbsp;{{if .Next}}<a class="next" href="{{.Next.Permalink}}">{{.Next.Title}} <i class="icon-arrow-right"></i></a>{{end}}
        </section>
    </div>

新建 layouts\partials\prevnext_aside.html，代码如下

	{{ $baseurl := .Site.BaseURL }}

	<aside id="prevnext">
	{{ partial "prevnext.html" . }}
	</aside>

	<meta itemprop="wordCount" content="{{ .WordCount }}">
	<meta itemprop="datePublished" content="{{ .Date.Format "2006-01-02" }}">
	<meta itemprop="url" content="{{ .Permalink }}">

在 layouts\_default\single.html

	<section id="main">
	  <h1 itemprop="name" id="title">{{ .Title }}</h1>
	  {{ partial "meta_aside.html" . }}
	  <div>
	        <article itemprop="articleBody" id="content">
	           {{ .Content }}
	        </article>
	  </div>
	</section>

	{{ partial "disqus.html" . }}
	{{ partial "footer.html" . }}

修改为

	<section id="main">
	  <h1 itemprop="name" id="title">{{ .Title }}</h1>
	  {{ partial "meta_aside.html" . }}
	  <div>
	        <article itemprop="articleBody" id="content">
	           {{ .Content }}
	        </article>
	  </div>
	{{ partial "prevnext_aside.html" . }}
	</section>

	{{ partial "disqus.html" . }}
	{{ partial "footer.html" . }}

在 static\css\style.css

	#main>div,footer>div,#meta>div,#content,#comments>div{position:relative;text-align:left;margin:0 auto;word-wrap:break-word}

	#next,#prev{width:50% !important;padding-top:2em}

	#main>div,footer>div,#meta>div,#meta>section,#content,#comments>div{max-width:38em}

修改为

	#main>div,footer>div,#meta>div,#content,#comments>div,#prevnext>div{position:relative;text-align:left;margin:0 auto;word-wrap:break-word}

	#next,#prev{width:50% !important;padding-top:2em;float:left;}

	#main>div,footer>div,#meta>div,#meta>section,#content,#comments>div,#prevnext>div{max-width:100%}

在浏览器查看效果
![](/images/20161202/hugo_2_8_3_02.png)

### 2.9 修改字号大小

在 static\css\style.css

	body{font-size:0.9em;line-height:1.8em}
	h1{font-size:1.6em;line-height:1.8em}
	h2{font-size:1.4em;line-height:1.8em}
	h3{font-size:1.2em;line-height:1.8em}
	h4{font-size:1.1em;line-height:1.8em}
	h5{font-size:1em;line-height:1.8em}

	h1#title{font-size:1.6em !important;line-height:1.8em}
	h1,h2{font-size:1.6em;line-height:1.8em}
	h2{font-size:1.4em;font-weight:normal}
	h3{margin:1.3em 0 1.5em}

	.post-meta{color:#34495e;font-size:90%}

	body>footer p{margin:0;padding:0;font-size:90%;line-height:1.5em}

	body>section,body>aside,body>footer,body>.content{font-size:0.9em;line-height:1.8em}

	body>section,body>aside,body>.content,body>footer,#comments{margin:1em 7.14286% 0 28.57143%;font-size:0.9em;line-height:1.8em;*zoom:1}

	body>section,body>aside,body>.content,body>footer,#comments{margin:1em 21.42857% 0 21.42857%;font-size:0.9em;line-height:1.8em;*zoom:1}

### 2.10 修改网站标题

![](/images/20161202/hugo_2_10_01.png)

在 layouts\partials\header.html

	<title> {{ .Title }} - spf13.com </title>

将网站标题spf13.com修改为imkind.github.io

	<title> {{ .Title }} - imkind.github.io </title>

![](/images/20161202/hugo_2_10_02.png)

### 2.11 修改og标签
摘自知乎：
`og是一种新的HTTP头部标记，即Open Graph Protocol，用了Meta Property=og标签，就是你同意了网页内容可以被其他社会化网站引用等`

在 layouts\partials\meta.html 将大神spf13相关的信息修改为自己的，或者注释掉这一部分

	<!-- open graph -->
	<meta property="og:type" content="article"/>
	<meta property="og:description" content="{{ .Description }}"/>
	<meta property="og:title" content="{{ .Title }} : spf13.com"/>
	<meta property="og:site_name" content="spf13 is Steve Francia"/>
	<meta property="og:image" content="" />
	<meta property="og:image:type" content="image/jpeg" />
	<meta property="og:image:width" content="" />
	<meta property="og:image:height" content="" />
	<meta property="og:url" content="{{ .Permalink }}">
	<meta property="og:locale" content="en_US">
	<meta property="article:published_time" content="{{ .Date.Format "2006-01-02" }}"/>
	<meta property="article:modified_time" content="{{ .Date.Format "2006-01-02" }}"/>

	<!--Twitter Cards-->
	<meta name="twitter:card" content="summary">
	<!--<meta name="twitter:card" content="summary_large_image">-->
	<meta name="twitter:site" content="@spf13">
	<meta name="twitter:title" content="{{ .Title }} : spf13.com">
	<meta name="twitter:creator" content="@spf13">
	<meta name="twitter:description" content="{{ .Description }}">
	<meta name="twitter:image:src" content="">
	<meta name="twitter:domain" content="spf13.com">

### 2.12 多说评论系统
spf13.com默认使用了[Disqus](https://disqus.com/)评论系统，我去注册并在博客配置之后，无法显示评论区，而且在打开博文时因为连接Disqus服务器严重拖慢速度，于是更换了国产的[多说](http://duoshuo.com/)评论系统，相对于内置了Disqus，多说评论系统的植入稍微复杂一点。

#### 2.12.1 注册[多说](http://duoshuo.com/)评论系统
打开多说首页，点击“登录”，使用SNS账号登录，我选择了微博。
![](/images/20161202/hugo_2_12_1_01.png)

#### 2.12.2 创建站点
登录之后就跳转到站点配置的页面
![](/images/20161202/hugo_2_12_2_01.png)

#### 2.12.3 获取植入代码
工具——>获取代码——>稳定版
![](/images/20161202/hugo_2_12_3_01.png)

#### 2.12.4 植入多说代码
在 layouts\partials\disqus.html

	<aside id=comments>
	    <div><h2> Comments </h2></div>

	     {{ template "_internal/disqus.html" . }}

	</aside>

修改为

	<aside id=comments>
	    <div><h2> Comments </h2></div>

	     <!-- {{ template "_internal/disqus.html" . }} -->

	     <!-- 多说评论框 start -->
		<div class="ds-thread" data-thread-key="{{ .URL }}" data-title="{{ .Title }}" data-url="{{ .Permalink }}"></div>
		<!-- 多说评论框 end -->
		<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
		<script type="text/javascript">
		var duoshuoQuery = {short_name:"imkind"};
			(function() {
				var ds = document.createElement('script');
				ds.type = 'text/javascript';ds.async = true;
				ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
				ds.charset = 'UTF-8';
				(document.getElementsByTagName('head')[0]
				 || document.getElementsByTagName('body')[0]).appendChild(ds);
			})();
			</script>
		<!-- 多说公共JS代码 end -->

	</aside>

#### 2.12.5 设置duoshuoShortname
在 config.toml

	disqusShortname = ""

修改为

	duoshuoShortname = "imkind"

在浏览器中博文下方查看效果

![](/images/20161202/hugo_2_12_5_01.png)


### 2.13 代码高亮

#### 2.13.1 使用在线js(加载速度慢，不推荐使用)
参考：[nanshu.wang](http://nanshu.wang/)的教程[Hugo静态网站生成器中文教程](http://nanshu.wang/post/2015-01-31/)

在 layouts\partials\head_includes.html 添加

	<script src="https://yandex.st/highlightjs/8.0/highlight.min.js"></script>
	<link rel="stylesheet" href="https://yandex.st/highlightjs/8.0/styles/default.min.css">
	<script>hljs.initHighlightingOnLoad();</script>

在浏览器查看效果

#### 2.13.2 使用本地配置[highlight.js](https://highlightjs.org/)（推荐使用）
参考：[http://newoxygen.github.io](http://newoxygen.github.io) 的教程 [hugo快速建站](http://newoxygen.github.io/post/hugo%E5%BF%AB%E9%80%9F%E5%BB%BA%E7%AB%99/#2.2)

##### 2.13.2.1 下载 [highlight.js](https://highlightjs.org/)
打开[highlight.js](https://highlightjs.org/)首页，点击 [Get version 9.8.0](https://highlightjs.org/download/)，勾选常用的编程语言，点击“Download”下载，解压

	$ ls highlight/
	CHANGES.md  highlight.pack.js  LICENSE  README.md  README.ru.md  styles/

##### 2.13.2.2 植入highlight.js

复制 highlight.pack.js 到 static\static\js

重命名 styles 为 highlight，并复制到 static\static\css

在layouts\partials\header.html 添加

	<!-- Highlight.js -->
	<script src="{{ .Site.BaseURL }}static/js/highlight.pack.js"></script>
	<link rel="stylesheet" href="{{ .Site.BaseURL }}static/css/highlight/github.css">
	<script>hljs.initHighlightingOnLoad();</script>

可根据个人爱好调用 static\static\css\highlight 中的css样式。

#### 2.14 植入百度统计

注册[百度统计](http://tongji.baidu.com)，在“管理”中“新增网站”

![](/images/20161202/hugo_2_14_01.png)

拷贝“统计代码”至 layouts\partials\header.html 的`</head>`标签前

	<script>]
	var _hmt = _hmt || [];
	(function() {
	  var hm = document.createElement("script");
	  hm.src = "https://hm.baidu.com/hm.js?3d1fd52b11dc0bde2abdc4760452f3dc";
	  var s = document.getElementsByTagName("script")[0];
	  s.parentNode.insertBefore(hm, s);
	})();
	</script>

#### 2.15 植入Google Analytics(分析)

注册[Google Analytics (分析)](http://www.google.cn/intl/zh-CN_ALL/analytics/features/analysis-tools.html)，在“管理”——“媒体资源”中“创建新媒体资源”

![](/images/20161202/hugo_2_15_01.png)

拷贝“跟踪代码”至 layouts\partials\header.html 的`</head>`标签前

	<script>
	  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
	  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
	  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
	  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');

	  ga('create', 'UA-86857821-1', 'auto');
	  ga('send', 'pageview');
	</script>

### 3. 部署hugo到Github pages个人主页
[Github pages](https://pages.github.com/#user-site)有两种类型，个人主页（User site）和项目主页（Project site）。个人主页一个账号只能有一个，必须以[your-github-account].github.io命名来创建repository。

#### 3.1 在Github新建[your-hugo-project-name]仓库，用于托管hugo静态博客系统源代码

	https://github.com/imkind/IMkind_blog_hugo

#### 3.2 在Github新建[your-github-account].github.io仓库，用于存放hugo生成得静态博客站点文件

	https://github.com/imkind/imkind.github.io

#### 3.3 创建本地的git仓库

	$ git init

#### 3.4 添加远程库，把本地git仓库同远程库关联起来

	$ git remote add origin git@github.com:[your-github-account]/[your-hugo-project-name].git

即

	$ git remote add origin git@github.com:imkind/IMkind_blog_hugo.git

#### 3.5 查看项目下是否有生成了静态页面的`public`目录，如果有删除掉（只要执行`hugo`即可重新生成），因为下一步配置子项目时，会再创建一个public目录

	$ rm -rf public/

##### 3.6 把[your-github-account].github.io设置为[your-hugo-project-name]的子项目，命令会`clone`远程仓库[your-github-account].github.io到本地并重命名为`public`

	$ git submodule add git@github.com:[your-github-account]/[your-github-account].github.io.git  public

即

	$ git submodule add git@github.com:imkind/imkind.github.io.git public
	Cloning into 'D:/HelloWorld/hugo/IMkind_blog_hugo/public'...
	warning: You appear to have cloned an empty repository.
	fatal: You are on a branch yet to be born
	Unable to checkout submodule 'public'

#### 3.7 把pulic目录写入.gitignore文件，在之后提交hugo项目源代码时就会忽略public目录

	$ echo "/public" > .gitignore

#### 3.8 把submodule配置写入.gitmodules文件。如ls -A看不到该文件，可自行创建并添加内容。

	$ vim .gitmodules

	[submodule "public"]
		path = public
		url = git@github.com:imkind/imkind.github.io.git

#### 3.9 初次提交至github远程仓库,使用了`-u`参数,之后改动代码提交到github，只需`git push origin master`

	$ git add -A

	$ git commit -m "first commit"

	$ git push -u origin master
	Counting objects: 379, done.
	Delta compression using up to 4 threads.
	Compressing objects: 100% (368/368), done.
	Writing objects: 100% (379/379), 1.15 MiB | 0 bytes/s, done.
	Total 379 (delta 157), reused 0 (delta 0)
	remote: Resolving deltas: 100% (157/157), done.
	To github.com:imkind/IMkind_blog_hugo.git
	 * [new branch]      master -> master
	Branch master set up to track remote branch master from origin.

#### 3.10 生成静态站点到public目录下

	$ hugo

#### 3.11 初次提交pubic目录下的静态站点内容到远程仓库

	$ cd public

	$ git add -A

	$ git commit -m "first post"

	$ git push -u origin master
	Counting objects: 219, done.
	Delta compression using up to 4 threads.
	Compressing objects: 100% (210/210), done.
	Writing objects: 100% (219/219), 1.16 MiB | 51.00 KiB/s, done.
	Total 219 (delta 71), reused 0 (delta 0)
	remote: Resolving deltas: 100% (71/71), done.
	To https://github.com/imkind/imkind.github.io.git
	 * [new branch]      master -> master
	Branch master set up to track remote branch master from origin.

### 4. 部署成功，在浏览器访问 https://[your-github-account].github.io 就看到自己的博客了。
![](/images/20161202/hugo_4_01.png)

#### *参考*
* [Steve Francia](http://spf13.com/)
* [Hugo中文文档](http://www.gohugo.org/)
* [使用hugo搭建个人博客站点](http://www.gohugo.org/post/coderzh-hugo/)
* [Hugo静态网站生成器中文教程](http://nanshu.wang/post/2015-01-31/)
* [利用 Hugo & GitHub 搭建个人博客静态网站](http://blog.bpcoder.com/2015/12/hugo-create-blog/)
