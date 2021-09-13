---
title: k8s mysql demo
date: 2021-05-25 14:59:08
tags: kubernetes
---



> mysql-rc.yaml

```yaml
apiVersion: v1
kind: ReplicationController # 副本控制器 RC
metadata: # 元数据，相当于属性
	name: mysql	# RC 的名称，全局唯一
spec: # 规范？更像是配置
		replicas: 1 # Pod 副本的期待数量
		selector:
			app: "mysql" # 符合目标的 Pod 拥有此标签 # 用于匹配 Pod
		template: # 模版
			metadata:
				labels:
					app: "mysql" # Pod 副本拥有的标签，需要和 RC.spec.selector 符合，否则会不停创建 Pod 以满足 replicas 的要求
			spec:
				containers: # Pod 内容器的定义部分 # 一个 Pod 可能由多个容器组成
				- name: mysql # 容器的名字
					image: mysql # 容器对应的镜像
					ports:
					- containerPort: 3306
					env:
						- name: MYSQL_ROOT_PASSWORD
							value: "123456"	
```

> ，需要特别注意的是：这里的labels必须匹配之前的spec.selector，否则此RC每创建一个无法匹配Label的Pod，就会不停地尝试创建新的Pod，陷入恶性循环中

