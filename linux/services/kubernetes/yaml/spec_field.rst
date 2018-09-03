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

^^^^^^^^^^^^^^
nodeSelector
^^^^^^^^^^^^^^

* pod将倾向运行在包含这些label的node上。

^^^^^^^^^^^^^^
nodeName
^^^^^^^^^^^^^^

* pod运行在指定nodename的节点上。


