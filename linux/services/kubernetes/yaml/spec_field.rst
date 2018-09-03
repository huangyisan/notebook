=============================
spec field
=============================

**不同的资源，spec字段不尽相同，用于定义资源规格，代表了对资源状态的期望**



-------------------
pod资源类型
-------------------

^^^^^^^^^^^^^
containers
^^^^^^^^^^^^^

**pod spec中必须存在的字段**

* 定义属于当前pod的container。
* 多个container用list方式定义。用 ``-`` 。
* 不可以被更新。

containers下的field:
    * name 表示container的名称。
    * image 表示该container使用的镜像。
    * imagePullPolicy 表示镜像获取策略。
        * 默认值为Always，表示总是去镜像仓库下载。
        * Never, 本地有就用，没有就不下载，也不进行后续处理。
        * IfNotPresent, 本地有就使用，如果没有则去镜像仓库下载。
    * command 表示当启用该镜像的时候，同时需要执行的命令。


