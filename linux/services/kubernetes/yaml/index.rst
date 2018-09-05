=============================
yaml
=============================

**用于定义k8s的资源清单，apiserver自动将其转换为json格式，然后再提交**

**可以使用** ``kubectl explain $source.$key1.$key2`` **来查看具体的用法和含义，资源对象若包含了** ``-required-`` **则表示必须存在的字段。若字段存在** ``cannot be update`` **则表示不支持热更新**

.. code-block:: python

    [root@k8s_master ~]# kubectl explain pod.kind
    KIND:     Pod
    VERSION:  v1

    FIELD:    kind <string>

    DESCRIPTION:
         Kind is a string value representing the REST resource this object
         represents. Servers may infer this from the endpoint the client submits
         requests to. Cannot be updated. In CamelCase. More info:
         https://git.k8s.io/community/contributors/devel/api-conventions.md

* 大小写敏感。
* 使用缩进表示层级关系。
* 缩进时不允许使用Tal键，只允许使用空格。
* 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可。
* ”#” 表示注释，从这个字符一直到行尾，都会被解析器忽略。
* k8s中的yaml数据格式只有 ``list`` 和 ``map`` 。
* 顶格使用 ``---`` 分割定义多个资源。

.. toctree::
   :maxdepth: 1

   5_important_field
   metadata_field
   spec_field
   status_field
