---
title: pm2 管理所有应用
date: 2022-05-30 10:18:21
tags: [pm2, nodejs]
---



## 关于 pm2

PM2 is a daemon process manager that will help you manage and keep your application online 24/7



## 基本用法

```shell
pm2 start app.js
pm2 stop [name, id, all]
pm2 restart [name, id, all]
pm2 kill [name, id, all]
```



## 配置文件

pm2  支持根据配置文件启动，一般有三种:

1. json
2. yaml
3. js 文件 （确切说就是 nodejs 脚本）



其实对参数而言都是一样的，官方推荐 js 文件，原因是 js 文件可能容易实现自动化配置的一些技巧（本质就是 nodejs 脚本）。如果配置比较简单也可以选用 yaml。**个人不太推荐 json，原因是 json 不支持注释**。

之后**所有范例都是 js 脚本，请勿把它与 json 搞混淆**。



## pm2 启动 nodejs 服务

```javascript
module.exports = {
  apps: [
    {
      name: 'app_name',
      script: 'app.js',
      watch: true,
      watch_delay: 1e4,
      cwd: '/home/maple/app'，
      env: {
      	NODE_ENV: 'PROD'
    	}
    }
}
```



- name：app 名称
- script：js 文件
- env：环境变量
- watch
  - 是否监听，用于 dev 开发，一旦文件变动就会自动重启
  - 这个参数也支持非 js 应用
  - 这个参数支持传入数组，直接监听需要修改的文件夹（推荐）。
  - ignore_watch：数组。忽略的文件夹，比如 node_modules, logs
    - watch 参数如果传入数组，那这个参数应该不生效（待验证）。
    - 如果包含多文件最好传入 ignore_watch，比如 `.git` 文件夹。 因为系统会有 watch 文件数量限制。一旦超过限制，会出现 watch 无效的情况。
  - watch_delay：如果同时会出现多个文件修改，并且不期望应用多次重启（例如 git pull），可以传入此参数，此参数控制重启延迟，这样在延迟时间内的修改都作为一次重启。

## pm2 启动二进制文件

```javascript
module.exports = {
	name: 'minio',
	exec: 'minio',
	env: {
		MINIO_SERVER_URL: 'https://minio.test.cc:5000',
		MINIO_BROWSER_REDIRECT_URL: 'https://minio-console.test.cc'
	},
	args: 'server /home/maple/oss --console-address :9090 --address :9000'
}
```



二进制文件，其实也是 linux 环境下的执行程序。

如果非 PATH 目录下的二进制包，也可以通过传入 pwd 参数或者直接使用绝对路径调用。

注意配置参数名是 `exec` 和 `args`。

> 如果配置 script 参数，会默认需要启动器，也就是会默认会使用 `/bin/sh` 来调用。



## pm2 启动带文件后缀的脚本

如果是 shell 脚本，启动 nodejs 相同，pm2 会自动调用 /bin/sh 来执行。

> 如果偷懒，可以把任意启动写成 shell 脚本，直接执行就可以了。



但是如果直接启动 py 文件这类脚本文件，需要配置 interpreter 参数，比如启动 Python 应用:

```javascript
{
	name: 'chinese-dos-ga',
	script: 'app.py',
	cwd: '/home/maple/app/chinese-dos-ga',
	interpreter: '/usr/bin/python3'
}
```



## pm2 启动不带文件后缀的脚本

```javascript
module.exports = {
	name: 'pgadmin4',
  exec: 'gunicorn',
	args: '--bind unix:/tmp/pgadmin4.sock --workers=1 --threads=25 --chdir /home/maple/.local/lib/python3.8/site-packages/pgadmin4 pgAdmin4:app',
	interpreter: 'python3'
}
```



对于不带文件后缀的脚本，我们一般也把他当做二进制来执行。但是对于上述的配置，在没有配置 interpreter 的前提下，不会正常执行。



原因是 pm2 可能会解析执行的文件是否为二进制包，如果是，直接调用。如果不是二进制包，则调用 `/bin/bash` 来执行。这样会造成类似 `gunicorn` 获取不到 `args` 参数。

```shell`
bash gunicorn --bind xxxx
```



这时候只能来替换 interpreter，变成

```
python3 gunicorn --bind xxxx
```

就能正常运行。



> 也就是，并非启动不了，只是为了防止 args 参数被 bash 吞掉。如果对于没有 args 参数，则直接用 bash 启动也不会有什么问题。