====================
common grammar
====================

--------------------
1、yaml写法特点
--------------------
* 大小写敏感。
* 使用缩进表示层级关系。
* 缩进时不允许使用Tal键，只允许使用空格。
* 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可。
* ”#” 表示注释，从这个字符一直到行尾，都会被解析器忽略。
* k8s中的yaml数据格式只有list和map。

-------------------
2、常见key: value
-------------------

**可以使用** ``kubectl explain $source.$key1.$key2`` **来查看具体的用法和含义。**

.. code-block:: python

    [root@k8s_master ~]# kubectl explain pod.kind
    KIND:     Pod
    VERSION:  v1

    FIELD:    kind <string>

    DESCRIPTION:
         Kind is a string value representing the REST resource this object
         represents. Servers may infer this from the endpoint the client submits
         requests to. Cannot be updated. In CamelCase. More info:
         https://git.k8s.io/community/contributors/devel/api-conventions.md#types-kinds


^^^^^^^^^^^^^^^^^^^
2.1、apiVersion
^^^^^^^^^^^^^^^^^^^

* 写法为apiVersion: $GROUP_NAME/$VERSION，如果$GROUP_NAME为core，则可以省略。
* 不同k8s版本以及不同资源是对应不同apiVersion的。
* 可以通过命令 ``kubectl explain $source`` 查看当前版本的资源应该选择何种apiVersion

| 参考：
| https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.11/
| https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html

^^^^^^^^^^^^^^^^^^^
2.2、kind
^^^^^^^^^^^^^^^^^^^

* 表示资源类型。

^^^^^^^^^^^^^^^^^^^
2.3、metadata
^^^^^^^^^^^^^^^^^^^

* 表示资源的元数据。
* metadata下还有更多的嵌套数据。

^^^^^^^^^^^^^^^^^^^
2.4、spec
^^^^^^^^^^^^^^^^^^^

* 定义资源规格，目标期望状态。
* spec下还有更多的嵌套数据。

^^^^^^^^^^^^^^^^^^^
2.5、status
^^^^^^^^^^^^^^^^^^^

* 只读，显示当前状态。
* status的状态会无限趋近spec定义的目标期望状态。