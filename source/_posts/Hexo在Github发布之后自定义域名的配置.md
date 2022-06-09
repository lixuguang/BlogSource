---
title: Hexo在Github发布之后自定义域名的配置
date: 2022-06-09 12:40:40
categories: 
  - 杂七杂八
  - 博客技巧
tags: 
  - Hexo
---

> 作者：李旭光
> 引用请标明出处

# Hexo在Github发布之后自定义域名的配置

进到你的博客发布所在仓库，如：
![](/attachments/Clipboard_2022-06-09-12-57-48.png)
选择`Settings`，然后是`pages`，滚动页面找到`Custom domain`，将你自己的域名绑定进去保存一下就好了。
![](/attachments/Clipboard_2022-06-09-12-59-28.png)

在你自己的域名商处用CNAME把你自己的域名指定到你自己的仓库page地址上比如我的就是`lixuguang.github.io
Public`，这样你的`GitHub Page`就可以通过自己的域名进行访问了。


## `Hexo`在`Github`发布之后自定义域名会被清空的问题
使用 `GitHub Page` 功能将博客托管在了 `GitHub` 上，并配置 `CNAME` 将自己的域名解析了过去，但是发现一个问题，每次 `hexo deploy` 之后，`custom domain` 会被重置失效。

### 解决方案
在 `Hexo` 生成的博客的 `source` 目录下（**注意这个是你的博客源码所在仓库，并不是发布的仓库**）新建一个 `CNAME` 文件，然后在这个文件中填入你的域名，这样就不会每次发布之后，`GitHub Page` 里的 `custom domain` 都被重置掉啦。
