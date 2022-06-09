---
title: 给博客添加基于github-issue的评论系统
date: 2017-10-12 10:50:21
categories: 
	- 杂七杂八
	- 博客技巧
tags: 
	- Hexo
---
> 作者：李旭光
> 引用请标明出处


# 前言
上篇文章介绍了[如何利用github+hexo+next制作一个博客系统](https://lixuguang.github.io/2017/10/11/use-GitHub-Hexo-Next-make-blog/)，但是由于这个博客系统是基于静态页面的，所以无法完成评论功能，博客怎么可以没有评论功能呢，当然next主题提供了几个选项，但是国内你知道的，提供的几个评论功能都不是太好用，于是就上网找了好多资料，功夫不负有心人，让我找到了一个名曰gitment的基于github-issue的评论插件，下面就介绍大家如何使用这个插件。
<!-- more -->
## 第一步 注册一个小程序（OAuth Application）
点击[https://github.com/settings/applications/new](https://github.com/settings/applications/new)注册
- Application name 应用名称 这里随便写，我写的就是gitment
- Homepage URL 主页地址，你可以写你的博客地址，我写的是https://lixuguang.github.io/
- Application description 应用描述，这里随便写点什么，反正是自己用。
- Authorization callback URL 这个比较重要，请填写你的博客地址，我的是https://lixuguang.github.io/

点击确定以后你会获得两个关键信息，下一步配置时会用到
- Client ID
- Client Secret

## 第二步 修改主题配置文件，添加gitment评论功能
因为用的是next主题，所以配置文件地址如下：
**themes/next/_config.yml**

### 1、在其中添加:


```
# Gitment
# Introduction: https://imsun.net/posts/gitment-introduction/
gitment:
  enable: true
  githubID: yourid // 我的是lixuguang
  repo: yourrepo // 我的是lixuguang.github.io 必须跟githubID保持一致的用户名
  ClientID: yourid // 上面开通程序获得的ClientID
  ClientSecret: yoursecret // 上面开通程序获得的Client Secret
  lazy: false //是否需要点击展开评论才能可见评论，一般设置为false
```
**一定要注意空格，不然会报错的，别问我咋知道的**

### 2、然后在主题的配置语言环境的文件添加一句话
`en.yml`增加:

```
gitmentbutton: Show comments from Gitment
```

`zh-Hans.yml`增加:


```
gitmentbutton: 显示 Gitment 评论
```
如果是中文网站英文配置也可以不用写。
### 3、添加新的Dom结构
**修改主题layout/_partials/comments.swig**
在最后一个elseif分支后添加一个elseif分支：

```
{% elseif theme.gitment.enable %}
       {% if theme.gitment.lazy %}
         <div onclick="ShowGitment()" id="gitment-display-button">{{  __('gitmentbutton') }}</div>
         <div id="gitment-container" style="display:none"></div>
       {% else %}
         <div id="gitment-container"></div>
       {% endif %}
```
### 4、 在主题下layout/_third-party/comments/目录下中添加文件gitment.swig


```
{% if theme.gitment.enable %}
   {% set owner = theme.gitment.githubID %}
   {% set repo = theme.gitment.repo %}
   {% set cid = theme.gitment.ClientID %}
   {% set cs = theme.gitment.ClientSecret %}
   <link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
   <script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
   {% if not theme.gitment.lazy %}
       <script type="text/javascript">
           var gitment = new Gitment({
               id: window.location.pathname, 
               owner: '{{owner}}',
               repo: '{{repo}}',
               oauth: {
                   client_id: '{{cid}}',
                   client_secret: '{{cs}}',
               }});
           gitment.render('gitment-container');
       </script>
   {% else %}
       <script type="text/javascript">
           function ShowGitment(){
               document.getElementById("gitment-display-button").style.display = "none";
               document.getElementById("gitment-container").style.display = "block";
               var gitment = new Gitment({
                   id: document.location.href, 
                   owner: '{{owner}}',
                   repo: '{{repo}}',
                   oauth: {
                       client_id: '{{cid}}',
                       client_secret: '{{cs}}',
                   }});
               gitment.render('gitment-container');
           }
       </script>
   {% endif %}
{% endif %}
```
然后在主题下layout/_third-party/comments/index.swig文件中引入gitment.swig文件：

```
{% include 'gitment.swig' %}
```
### 在主题下source/css/_common/components/third-party/目录下添加gitment.styl文件
此配置文件为gitment的样式文件，需要修改样式可以在这里进行书写，这里修改一下按钮样式，另外将聊天框于文章框样式统一

```
#gitment-display-button{
     display: inline-block;
     padding: 0 15px;
     color: #0a9caf;
     cursor: pointer;
     font-size: 14px;
     border: 1px solid #0a9caf;
     border-radius: 4px;
 }
 #gitment-display-button:hover{
     color: #fff;
     background: #0a9caf;
 }
  #comments {
    margin: 0;
    padding: 40px;
    background: #fff;
    box-shadow: 0 2px 2px 0 rgba(0,0,0,0.12), 0 3px 1px -2px rgba(0,0,0,0.06), 0 1px 5px 0 rgba(0,0,0,0.12);
}
```
然后在主题下source/css/_common/components/third-party/third-party.styl文件中引入相应的CSS样式即可:

```
@import "gitment";
```
经过以上操作，gitment就被引入到你的博客里了。

现在就可以让大家对你写的文章进行评论啦，怎么样是不是又学到啦，喜欢我的文章就请关注我的github吧。
