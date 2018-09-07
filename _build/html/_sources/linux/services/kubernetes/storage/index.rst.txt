=============================
storage
=============================

**kubernete存储，属于pod级别**
**存储卷在pod上定义，容器要使用，则需要在容器中挂载和绑定。**
**先挂载存储卷，再启动容器。**


存储卷不属于容器，属于pod

存储卷类型：
   * emptyDir 通过节点node,挂载到pod，随着pod的释放而释放存储卷。当临时目录或缓存用。同个pod内多个容器若选用同个存储卷挂载，则这些个容器都能相互访问到挂载的路径
   * hostPath 通过节点node,挂载到pod，在节点node上持久化存储，只有节点node挂了才会受到影响。
   * 网络存储 脱离节点node的存储，不会收到node状态的影响，持久化。
      * SAN 协议 iSCSI 等。
      * NAS 协议 nfs cifs 等。
   * 分布式存储 glusterfs rbd cephfs。
   * 云存储 EBS OSS 等。

kuerbetes支持的存储卷类型：
   * kubectl explain pods.sepc.volumes 查看。

**在kubernetes中使用存储卷的步骤:**

| 1、在pod上定义volumes，voluems指定关联到哪个存储设备。
| 2、在容器使用volumeMounts，将存储卷挂载。



.. toctree::
   :maxdepth: 1
   
   pvc

