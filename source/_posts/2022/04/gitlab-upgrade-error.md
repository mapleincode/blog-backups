---
title: gitlab 升级失败 or gitlab-ctl reconfigure 失败
date: 2022-05-25 11:12:27
tags: gitlab
---



## 问题

通过 apt 升级 gitlab-ce 发生错误。提示 gitlab-ctl reconfigure 失败。

```
Running handlers:
There was an error running gitlab-ctl reconfigure:

rails_migration[gitlab-rails] (gitlab::database_migrations line 51) had an error: Mixlib::ShellOut::ShellCommandFailed: bash[migrate gitlab-rails database] (/opt/gitlab/embedded/cookbooks/cache/cookbooks/gitlab/resources/rails_migration.rb line 16) had an error: Mixlib::ShellOut::ShellCommandFailed: Command execution failed. STDOUT/STDERR suppressed for sensitive resource

Running handlers complete
Chef Infra Client failed. 0 resources updated in 52 seconds
```



## 原因

gitlab 自带的 bug ，造成 db 迁移失败。



## 解决方案

执行 

```shell
sudo gitlab-rake db:migrate
```

返回一段错误提示，注意其中一段：

```
...
Finalize it manualy by running

	sudo gitlab-rake gitlab:background_migrations:finalize[ProjectNamespaces::BackfillProjectNamespaces,projects,id,'[null\,"up"]']

For more information, check the documentation
..
```

执行:

```shell
	sudo gitlab-rake gitlab:background_migrations:finalize[ProjectNamespaces::BackfillProjectNamespaces,projects,id,'[null\,"up"]']
	
	# 成功之后执行 sudo gitlab-rake db:migrate
	sudo gitlab-rake db:migrate
	
	# 成功之后执行 sudo gitlab-ctl reconfigure
```

