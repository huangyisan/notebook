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

containers下的field:
    * name 表示container的名称。
    * image 表示该container使用的镜像。
        * env 传入镜像的变量，多个变量用list的方式。
            * name 变量名
            * value 变量值
    * imagePullPolicy 表示镜像获取策略。
        * 如果镜像标签为latest，则默认值为 ``Always`` ，表示总是去镜像仓库下载。
        * ``Never`` , 本地有就用，没有就不下载，也不进行后续处理。
        * ``IfNotPresent`` , 本地有就使用，如果没有则去镜像仓库下载。
    * command 表示当启用该镜像的时候，同时需要执行的命令, 将忽略镜像中的entrypoint和cmd。
        * 类似dockerfile中的entrypoint。
        * 如果没有指定command，镜像中存在entrypoint，则镜像启动的时候执行entrypoint。
        * 他不会运行在shell中，所以如果要用shell运行，则需要指定"/bin/sh"。
            * .. code-block:: python

                command:
                - "/bin/sh"
                - "-c"
                - "echo hello"
    * args 表示启用镜像的时候，传递给程序的参数。(ps: dockerfile中，如果只有cmd，则运行cmd，如果entrypoint和cmd共存，则cmd作为参数传递给entrypoint。)
        * 默认如果不提供args，则镜像中的cmd作为参数传递给镜像中的entrypoint。
        * 如果指定了args，则args将取代cmd，作为参数传递给镜像中的entrypoint。
        * args中若要定义变量，则使用$(VAR_NAME)。变量逃逸，$$(VAR_NAME)。
        * `command args的情况参考官方文档。 <https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/#notes>`_
    * ports 表示容器内需要暴露出去的端口，可以多个，用list方式。 `这定义仅仅是信息性的定义，具体暴露哪个端口还是看容器镜像`。
        * containerPort: 信息性定义容器端口。
        * name: 信息性定义端口含义。
        * protocol: 没有定义则默认为tcp。
    * livenessProbe: 如果检测失败，则进行容器重启。
        * 包含三种探针，exec httpGet tcpSocket。
            * exec 执行命令的方式进行探测，根据返回值来确定失败还是成功。
                .. code-block:: python

                    exec:
                      command: ["test", "-e", "/tmp/healthy"]

            * httpGet 通过http的方式进行探测。
                .. code-block:: python

                    httpGet:
                      port: 80
                      path: /index.html

            * tcpSocket 通过tcp的方式进行探测。
        * failureThreshold 表示连续失败n次才算失败，防止探测抖动。默认3次。
        * periodSeconds 表示每次探测间隔时长，默认10s。
        * timeoutSeconds 表示每次探测超时时长，默认1s。
        * initialDelaySeconds 表示等待容器初始化时间，在这个时间后再进行探测，确保容器启动正常。默认容器启动就探测。
    * readinessProbe 表示容器服务是否正常的探测。如果探测失败，则在服务节点上移除这个容器。
    * lifecycle 用于容器启动和结束前后的钩子。
        * postStart 容器刚运行时执行的行为。
        * preStop 容器停止前执行的行为。

^^^^^^^^^^^^^^
nodeSelector
^^^^^^^^^^^^^^

* pod将倾向运行在包含这些label的node上。

^^^^^^^^^^^^^^
nodeName
^^^^^^^^^^^^^^

* pod运行在指定nodename的节点上。

^^^^^^^^^^^^^^^^^
restartPolicy
^^^^^^^^^^^^^^^^^

* 表示当pod挂了重启的策略。

restartPolicy的参数：
    * Always，一旦容器挂了，就重启，总是重启。默认为Always。
    * OnFailure，状态为错误的时候才会重启，正常关闭容器不会重启。
    * Never，从不重启容器。

^^^^^^^^^^^^^^
hostNetwork
^^^^^^^^^^^^^^

* 这个pod将直接使用主机的网络名称空间。

----------------------
ReplicaSet资源类型
----------------------

^^^^^^^^^^^^^^^^^^^
replicas
^^^^^^^^^^^^^^^^^^^

* 定义需要创建的副本数量


^^^^^^^^^^^^^^^^^^^
selector
^^^^^^^^^^^^^^^^^^^

* 定义标签选择器

包含了两种标签类型：
    * matchLabels
    * matchExpressions


^^^^^^^^^^^^^^^^^^^
template
^^^^^^^^^^^^^^^^^^^

* 定义pod模板

里面包含了两个字段，都是pod的属性，一个是 ``metadata`` ，一个是 ``spec`` ，具体可以参考pod中的对应字段解释。

**metadata字段下的label标签必须和ReplicaSet中的spec字段下的selector的标签对应,也就是被管理的pod和replicaset的标签要对应。**

-------------------------
Deployment资源类型
-------------------------

**虽然Deployment通过管理ReplicaSet,间接管理pod，但是在定义其yaml文件中是不存在ReplicaSet的。**

^^^^^^^^^^^^^^^^^^
strategy
^^^^^^^^^^^^^^^^^^

* 用于定义滚动更新时候的策略。

存在两个字段：
    * rollingUpdate 当type的类型为rollingUpdate的时候，该字段的内容才生效。
        * maxSurge 指定具体数量，或者百分比。
        * maxUnavailable 指定最多有多少个不可用。

    * type
        * Recreate 重建更新，删除一个创建一个的机制。
        * rollingUpdate 采用滚动更新机制。

^^^^^^^^^^^^^^^^^^^^^^^^
revisionHistoryLimit
^^^^^^^^^^^^^^^^^^^^^^^^

* 定义保留过去多少个副本，用于回滚。默认为10个。如果是0，则不保存。

^^^^^^^^^^^^^^^^^^^^^^^^
paused
^^^^^^^^^^^^^^^^^^^^^^^^

* 控制更新是否暂停。

^^^^^^^^^^^^^^^^^^^^^^^^
template
^^^^^^^^^^^^^^^^^^^^^^^^

* 定义pod模板。

**和ReplicaSet一样，定义的label必须和外层Deployment资源的selector的label一致。**


-------------------------
DaemonSet资源类型
-------------------------

**和ReplicaSet类似，但不存在 ``replicas`` 字段。**

^^^^^^^^^^^^^^^^^^^^^^^^
updateStrategy
^^^^^^^^^^^^^^^^^^^^^^^^

* 设定更新策略。

其和Deployment一样有着 ``type`` 字段。

    * type 设定更新类型，默认为 ``OnDelete`` ,再删除时更新。
    * rollingUpdate 滚动更新策略只有 ``maxUnvaliable``。


----------------------
Service资源类型
----------------------

^^^^^^^^^^^^^^^^^^^
ports
^^^^^^^^^^^^^^^^^^^

* 指定service某端口和后端容器端口建立关系。
    * name 表示port的名称。
    * nodePort 表示指定节点上的端口。 **前提是type类型为NodePort。**
    * port 被service暴露给外部，对外提供服务的端口。
    * targetPort pod容器端口。

^^^^^^^^^^^^^^^^^^^
selector
^^^^^^^^^^^^^^^^^^^

* 表示关联哪些pod资源。


^^^^^^^^^^^^^^^^^^^
clusterIP
^^^^^^^^^^^^^^^^^^^

* 手动指定service的ip。


^^^^^^^^^^^^^^^^^^^
type
^^^^^^^^^^^^^^^^^^^

* 指定service的类型。
    * ClusterIP 默认类型，仅用于集群内通信。此时ports字段只有 ``port`` 和 ``targetPort`` 有用。
    * NodePort 让集群外部的流量进行访问。通过每个Node上的IP和静态端口（NodePort）暴露服务。NodePort服务会路由到ClusterIP服务，这个ClusterIP服务会自动创建。通过请求<NodeIP>:<NodePort>，可以从集群的外部访问一个NodePort服务。
    * LoadBalancer 使用云厂商提供的负载均衡器，对外暴露服务。
    * ExternalName 把集群外部的服务引入集群内部。

^^^^^^^^^^^^^^^^^^^
sessionAffinity
^^^^^^^^^^^^^^^^^^^

* session亲和性，类似调度后端的权重。
    * None 类似轮训请求pod服务。
    * ClusterIP 根据ClusterIP指定调度。
