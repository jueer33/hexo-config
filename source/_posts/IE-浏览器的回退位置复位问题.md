---
title: IE 浏览器的回退位置复位问题
date: 2025-02-13 21:25:22
tags:
  - done
categories:
  - 报错处理
  - 前端
---

### 背景：

在一个长页面中有一个跳转，需要点击跳转后，使用浏览器的回退，希望可以回退后位置在之前的点击位置

最开始实现的时候是直接给跳转 a 绑定一个点击事件，点击时触发保存屏幕位置，保存到 sessionStorage

### 问题描述：

在谷歌浏览器中实现了功能，但是需要做一个 ie 浏览器的兼容，在 ie 浏览器中没有实现

### 解决过程

**@failed1：** 添加一个阻止事件触发，确保在保存位置后再进行跳转，无论如何这部分在 ie 浏览器中我都输出不了数据，看不到有没有触发点击事件，无论时 alert 还是 console.log

**@failed2：** 将 sessionStorage 换成 localStorage，同样实现不了

> 此外我之前 ie 内核都是 ie7 我没找到怎么换成 ie11

{% asset_img image.png %}

**@failed3：** 我就不要点击事件了吧，直接使用 onload 和 scroll 监听，在刷新，重载，以及用户跳转和滚动时候触发更新保存当前的位置，这样不论是回退还是刷新都会回到保存位置，使 sessionStorage 使得在新加载的时候是在页面顶部——谷歌可以，ie 还是不行

**@failed4：** 按照 **@failed3** 换成 localStorage 还是不行

**@failed5：** 我不用 a 标签了吧好吧？我直接 div 绑定 click 事件，查看是否触发，发现没有触发任何点击事件，其实也包括其他地方的点击事件，由于是服务端渲染所以线上的点击反而是没有问题的。除此之外我 360 浏览器 f12 发现可以调试的时候更换 ie 内核

**@成功：** 换回之前的 onload 和 scroll 监听，以及 a 标签，使用 localStorage 进存储，没有删除 click 事件，欸莫名其妙好了，快速 git push 查看免得等会儿又有问题，上线后没有问题实现了功能。

- 最后得代码是这样得：

  ```jsx

      mounted() {
        const scrollPosition = localStorage.getItem('scrollPosition')
        if (scrollPosition) {
          window.scrollTo(0, parseInt(scrollPosition, 10))
        }
        window.addEventListener('scroll', this.updateScrollPosition)
        window.addEventListener('beforeunload', this.updateScrollPosition)
      },

      beforeDestroy() {
        window.removeEventListener('scroll', this.updateScrollPosition)
        window.removeEventListener('beforeunload', this.updateScrollPosition)
      },
      methods: {
        updateScrollPosition() {
          const scrollTop = document.documentElement.scrollTop || document.body.scrollTop
          localStorage.setItem('scrollPosition', scrollTop)
        },
        savePosition(event, link) {
          event.preventDefault()
          const scrollTop = document.documentElement.scrollTop || document.body.scrollTop
          setTimeout(() => {
            sessionStorage.setItem('scrollPosition', scrollTop)
            window.location.href = link
          }, 100)
        }
      }
  ```

---

后来我想把 localStorage 换成 sessionStorage 以及把 click 事件删除，但是一换成 sessionStorage 就失败了，并且本地回退之前版本也失败了，就不敢动它了——能跑得代码不要动！

---

[我的 csdn](https://blog.csdn.net/m0_73518637/article/details/145548972?sharetype=blogdetail&sharerId=145548972&sharerefer=PC&sharesource=m0_73518637&spm=1011.2480.3001.8118)

[我的知乎](https://zhuanlan.zhihu.com/p/22733789446)

[我的小红书](https://www.xiaohongshu.com/explore/67a994c5000000001800b25e?xsec_token=GBYfMO1iBbTzG64eOWOUCKJ8ZTwW3twFfJxERc0W-IVls%3D&xsec_source=pc_creatormng)

[我的 notion](https://jueer33.notion.site/IE-1964cdfbb24080f38638d199eb5a5755?pvs=74)
