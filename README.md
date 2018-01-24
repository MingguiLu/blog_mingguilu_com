# blog_mingguilu_com
My blog powered by hexo

[欢迎使用Hexo文档](https://hexo.io/zh-cn/docs/)

####  安装运行环境

* 安装 Node.js

安装 Node.js 的最佳方式是使用 nvm。

cURL:

    $ curl https://raw.github.com/creationix/nvm/master/install.sh | sh
    
Wget:

    $ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh
安装完成后，重启终端并执行下列命令即可安装 Node.js。

    $ nvm install stable

* 安装Git

Linux (Ubuntu, Debian)：

    sudo apt-get install git-core

Linux (Fedora, Red Hat, CentOS)：

    sudo yum install git-core

* 安装Hexo

    $ npm install -g hexo-cli

#### 启动博客

* clone网站源代码

    git clone git@github.com:mingguilu/blog_mingguilu_com.git

    cd blog_mingguilu_com

* 安装node模块

    sudo npm install

* 启动博客

    hexo server

* 访问博客

   [http://localhsot:4000](ttp://localhost:4000)

####  Hexo插件

* hexo-browsersync 用于监听刷新

    npm install hexo-browsersync --save
    
    hexo server -w

* [hexo-admin](https://github.com/jaredly/hexo-admin) 提供类似后台的功能

    npm install hexo-admin --save

