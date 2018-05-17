title: gulp-autoprefixer 基本使用
date: 2017-12-14 16:06:09
tags: Gulp.js
---


[`转载 => 传送门`](http://www.ydcss.com/archives/94)
## 基本使用
>使用 gulp-autoprefixer 根据设置浏览器版本自动处理浏览器前缀。使用她我们可以很潇洒地写代码，不必考虑各浏览器兼容前缀。

```javascript
var gulp = require('gulp'),
    autoprefixer = require('gulp-autoprefixer');
 
gulp.task('testAutoFx', function () {
    gulp.src('src/css/index.css')
        .pipe(autoprefixer({
            browsers: ['last 2 versions', 'Android >= 4.0'],
            cascade: true, //是否美化属性值 默认：true 像这样：
            //-webkit-transform: rotate(45deg);
            //        transform: rotate(45deg);
            remove:true //是否去掉不必要的前缀 默认：true 
        }))
        .pipe(gulp.dest('dist/css'));
});
```

## gulp-autoprefixer 的 **browsers** 参数详解 
（[传送门](https://github.com/ai/browserslist#queries)）

● **last 2 versions**: 主流浏览器的最新两个版本

● **last 1 Chrome versions**: 谷歌浏览器的最新版本

● **last 2 Explorer versions**: IE的最新两个版本

● **last 3 Safari versions**: 苹果浏览器最新三个版本

● **Firefox >= 20**: 火狐浏览器的版本大于或等于20

● **iOS 7**: IOS7版本

● **Firefox ESR**: 最新ESR版本的火狐

● **> 5%**: 全球统计有超过5%的使用率

>发现上面规律了吗，相信这不难看出，接下来说说各浏览器的标识：

**Android** for Android WebView.

**BlackBerry or bb** for Blackberry browser.

**Chrome **for Google Chrome.

**Firefox or ff** for Mozilla Firefox.

**Explorer or ie** for Internet Explorer.

**iOS or ios_saf** for iOS Safari.

**Opera** for Opera.

**Safari** for desktop Safari.

**OperaMobile or op_mob** for Opera Mobile.

**OperaMini or op_mini** for Opera Mini.

**ChromeAndroid or and_chr**

**FirefoxAndroid or and_ff** for Firefox for Android.

**ExplorerMobile or ie_mob** for Internet Explorer Mobile.
