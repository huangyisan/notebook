=============================
service
=============================


-----------------
1、核心概念
-----------------

* service是一组pod的逻辑分组。是在pod上添加的一个中间层。
* service的实现是通过iptables的nat方式实现。
* 可以访问service,但由于其只是iptables的nat表项，所以无法被ping通。
* service可以理解为对一组pod访问的发布，可以发布在外部，那么外部请求就能访问到，可以发布在内部，那么其他组的pod就可以过来调用。
* 所以service有两种类型，一种是外部service，一种是内部service。
* CoreDns,kube-dns解析到server上的ClusterIP。
* headless service: 不存在ClusterIP(定义为None或者"")，那么解析则直接到service对应的后端pod上。
* cluster ip就是service的ip。
* service到pod有一个中间层————endpoints。
* 资源记录， SVC_NAME.NS_NAME.DOMAIN.LTD.
    * 服务名称.namespace名称.集群域名后缀。
    * 集群域名后缀默认为 svc.cluster.local.
        * 例子: redis.default.svc.cluster.local.  默认的namespace为default。
