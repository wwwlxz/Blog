title: Github搭建Blog
date: 2015-07-28 15:16:27
tags: Github使用
---

# Github搭建Blog

标签（空格分隔）： Github使用

---

##1、安装Github客户端
这个比较简单，这里就不再赘述了

##2、安装Node.js
下载Node.js，官网网址：
> https://nodejs.org/download/

安装完成之后配置环境变量：
```cmd
变量名：Path
变量值：;C:\Program Files\nodejs\node_modules\npm
```

##3、安装hexo
进入git shell，输入以下命令：
```cmd
npm install -g hexo
```

安装完成之后，在文件夹`C:\Users\lxz\Documents\GitHub\hexo`下初始化hexo，执行以下命令：
```cmd
hexo init
```

安装依赖包：
```cmd
npm install
```

本地查看
现在我们已经搭建起本地的hexo博客了，执行以下命令，然后到浏览器输入http://127.0.0.1:4000看看
```cmd
hexo generate
hexo server
```

##4、更改主题
###4.1、安装
```cmd
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

###4.2、配置
修改hexo根目录下的_config.yml : theme: yilia

###4.3、更新
进入themes目录
```cmd
cd themes/yilia
git pull
```

###4.4、配置
主题配置文件在主目录下的_config.xml:
```cmd
# Header
menu:
  主页: /
  所有文章: /archives
  # 随笔: /tags/随笔

# SubNav
subnav:
  github: "https://github.com/litten"
  weibo: "http://weibo.com/litten225"
  rss: "http://feed.feedsky.com/litten"
  # facebook: "/"
  # google: "/"
  # twitter: "/"
  # linkedin: "/"

rss: /atom.xml

# Content
excerpt_link: more
fancybox: true

# Miscellaneous

favicon: /favicon.png

avatar: "https://avatars2.githubusercontent.com/u/9530759?v=3&s=460"
share: true
duoshuo: true
```

##Tips
hexo支持更加简单的命令格式，比如：
```cmd
hexo g == hexo generate
hexo d == hexo deploy
hexo s == hexo server
hexo n == hexo new
```

参考文档：
> http://jingyan.baidu.com/article/d8072ac47aca0fec95cefd2d.html


