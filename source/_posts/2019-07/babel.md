---
title: Babel 学习笔记
date: 2018-05-22 17:32:01
tags: [ babel]
---
# Babel  学习

前段时间学习写 React.js ，使用了了 Dva.js 框架。然后在 `npm run dev`可以运行的情况下。发现静态文件编译一直出问题。提示 Uglify 打包失败。定位问题是使用了 ES6 写的包 [https://github.com/sorrycc/blog/issues/68](https://github.com/sorrycc/blog/issues/68)。



因为那个包是我自己写的，本来就是用于 Node 的包，这样就需要用 Babel 将 es6 转成 es5。



## 创建 `.babelrc`

在项目名称创建配置文件。

格式: 

```json
{
  "presets": [],
  "plugins": []
}
```

presets 就是转码规则。当然需要在项目里配置对应的规则转化包。

```sh
# ES2015转码规则
$ npm install --save-dev babel-preset-es2015

# react转码规则
$ npm install --save-dev babel-preset-react

# ES7不同阶段语法提案的转码规则（共有4个阶段），选装一个
$ npm install --save-dev babel-preset-stage-0
$ npm install --save-dev babel-preset-stage-1
$ npm install --save-dev babel-preset-stage-2
$ npm install --save-dev babel-preset-stage-3
```

比如我是要转换 es6（es2015）。

所以首先需要修改 `.babelrc`

```js
{
    "presets": [
        'es2015'
    ],
  "plugins": []
}
```

然后安装对应的转换包

```sh
npm install --save-dev babel-preset-es2015
```



## 转换

转换通过 `babel-cli` 来进行转换。 

`babel-cli` 可以安装全局也可以安装到项目中，通过 `npm`参数调用。

基本用法:

```bash
# 转码结果输出到标准输出
$ babel example.js

# 转码结果写入一个文件
# --out-file 或 -o 参数指定输出文件
$ babel example.js --out-file compiled.js
# 或者
$ babel example.js -o compiled.js

# 整个目录转码
# --out-dir 或 -d 参数指定输出目录
$ babel src --out-dir lib
# 或者
$ babel src -d lib

# -s 参数生成source map文件
$ babel src -d lib -s
```

## babel-node

是通过转换代码，直接在 Node 环境中运行 ES6。因为到现在 (v8.x)为止，稳定版本 Node 目前还未完全支持 ES6。所以使用 babel-node 可以直接运行这些文件。



```sh
$ babel-node es6.js
## 或者
$ babel-node
> (x => x * 2)(1)
2
```

也可以通过 npm 脚本，直接运行用 es6 写的脚本，而不需要事先转换。(虽然现在版本 Node 已经支持大部分的 ES6 的预发了)。



## babel-register

改写 require ，在引用时自动进行转码 ES6 文件。

```sh
$ npm install babel-register --save-dev
```



然后在代码运行最开始的地方引入`require('babel-register')`即可。

然代码后期引入就会自动对文件代码进行转换。



## babel-core

Babel 核心 API。

使用方法常见:

```js
var babel = require('babel-core');

// 字符串转码
babel.transform('code();', options);
// => { code, map, ast }

// 文件转码（异步）
babel.transformFile('filename.js', options, function(err, result) {
  result; // => { code, map, ast }
});

// 文件转码（同步）
babel.transformFileSync('filename.js', options);
// => { code, map, ast }

// Babel AST转码
babel.transformFromAst(ast, code, options);
// => { code, map, ast }
```

配置对象`options`，可以参看官方文档 <http://babeljs.io/docs/usage/options/>。

一个例子。

```js
var es6Code = 'let x = n => n + 1';
var es5Code = require('babel-core')
  .transform(es6Code, {
    presets: ['es2015']
  })
  .code;
// '"use strict";\n\nvar x = function x(n) {\n  return n + 1;\n};'
```

## babel-polyfill

Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如`Object.assign`）都不会转码。

举例来说，ES6在`Array`对象上新增了`Array.from`方法。Babel就不会转码这个方法。如果想让这个方法运行，必须使用`babel-polyfill`，为当前环境提供一个垫片。

```
# 安装
$ npm install --save babel-polyfill
```

```js
// 使用
import 'babel-polyfill';
// 或者
require('babel-polyfill');
```

Babel默认不转码的API非常多，详细清单可以查看 `babel-plugin-transform-runtime` 模块的[definitions.js](https://github.com/babel/babel/blob/master/packages/babel-plugin-transform-runtime/src/definitions.js)文件。



## 浏览器环境

在浏览器运行 Babel 的方法，即时编译。

Babel 6.0 开始不再提供。

```
$ npm install babel-core@old
```

引用

```html
<script src="node_modules/babel-core/browser.js"></script>
<script type="text/babel">
// Your ES6 code
</script>
```



## 在线转换

[REPL在线编译器](https://babeljs.io/repl/)



## 与其他工具配合

### ESLint

安装: 

```sh
$ npm install --save-dev eslint babel-eslint
```

.eslint

```json
{
  "parser": "babel-eslint",
  "rules": {
    ...
  }
}
```

package.json

```js
{
    "name": "my-module",
    "scripts": {
      "lint": "eslint my-files.js"
    },
    "devDependencies": {
      "babel-eslint": "...",
      "eslint": "..."
    }
  }
```



### Mocha

package.json

```json
{
    ...,
    "scripts": {
          "test": "mocha --ui qunit --compilers js:babel-core/register"
    }
}
```

参照阮一峰大师的文章 [http://www.ruanyifeng.com/blog/2016/01/babel.html]()
