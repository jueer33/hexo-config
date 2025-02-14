---
title: hexo-图片不显示
date: 2025-02-14 16:13:22
tags:
  - done
categories:
  - 报错处理
  - 其他
---

最近学习前端优化刷到一篇博客，[前端不删](https://godbasin.github.io/front-end-playground/front-end-work/front-end-days/a-book-with-one-story.html#%E8%BD%AC%E5%9C%BA%E7%94%B5%E5%AD%90%E4%B9%A6)，然后我就去部署自己的 github.io 了

……没错，学着学着玩儿起来了

[GitHub Pages + Hexo 搭建个人博客网站，史上最全教程](https://blog.csdn.net/yaorongke/article/details/119089190)

主要参考这个文章进行部署，其他都没啥问题但是我的 hexo 图片显示有问题

我设置了`post_asset_folder: true` 使用相对路劲来显示不管怎么样都没有成功

f12 查看 页面图片路径好像解析的时候有问题，他总会在路径前面加一个/

我本地路径为`![](image.png)`,线上图片如下: `/imge.png`

同理正常情况下的路径：`![](./image.png)`显示出来就是`/./image.png`

安装这个插件：`npm install hexo-asset-image --save`

使用这个方式来插入图片：`{% asset_img image.png %}`
