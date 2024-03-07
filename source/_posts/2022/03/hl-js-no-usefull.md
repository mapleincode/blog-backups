---
title: 记一次 highlight.js 不生效的问题
date: 2022-04-06 10:18:53
tags: 前端
---



## 问题

在折腾工作机的时候，惊奇的发现自己的博客的 code 块的语法高亮没了？！当时因为是主题的问题，还更换了 hexo 的主题。直到某一次查询到 csdn 的博客页面，发现博客的语法高亮也出现了相似的问题：字体的颜色显示成黑色。

 

> 先说解决方案，问题出在刚安装的 source code pro  字体上，国内某大站下载的字体存在问题，我后面去 google 官网下载了一份就正常了。

---



## 清理缓存 & 卸载

清理页面缓存，强制刷新页面，无法解决。

使用了隐形标签来测试是否被浏览器插件影响，无法解决。

清理浏览器缓存，无法解决。

卸载重装浏览器，无法解决。

更换电脑，发现别的电脑都是正常的。



咨询了前端同事大佬，按照他的意思来解释，页面的 css 显示应该是正常的。这个 code 模块的颜色应该是没问题的，那问题到底出在哪里？



我假设出现问题的原因一定不是硬件和浏览器的问题。我创建一个 html 文件:

```html
<html>
  <body>
    <code style="color:red;">test</code>
  </body>
</html>
```

结果这个文件可以正常显示颜色，那说明浏览器肯定没问题。

 然后回到出现问题的 csdn 页面，在最外层 body 进行修改，嵌入 `<code style="color:red;">test</code>` 这段代码，发现颜色显示依然正常。

那唯一的可能性就只剩下 css 影响，打开调试器，取消 css 选项。

然后筛选之后发现是 font family 影响的。

原因居然是字体。。。

因为 highlight.js 默认首选字体是 `source code pro`。就如前文所说的，国内某大站下载的字体存在缺陷，导致 color 不能正常显示。

解决方案也是重新安装对应的字体，或者卸载字体都能解决。
