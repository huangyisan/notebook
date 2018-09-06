=============================
ingress和ingress control
=============================

----------------------
核心概念
----------------------

* 当处于upstream后端的service对应的pod发生改变时，ingress将获悉改变后的pod信息，并将信息注入并保存到前端调度器的upstream相关配置中，触发前端调度器pod中主容器的进程重载，读取最新配置文件。
* ingress control会对nginx的配置文件进行修改。
* ingress可以部署为hostNetwork的方式，这样用户可以直接访问到ingress，然后ingress根据rule，将请求转发给backend指定的service，再由service递交给pod。
* 如果要使用https的方式，则需要用命令将证书创建为k8s的secret资源，同时在ingress的yaml中tls字段中配置该secret资源。