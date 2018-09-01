===========================
five important yaml field
===========================


---------------------
1、apiVersion
---------------------

* 写法为apiVersion: $GROUP_NAME/$VERSION，如果$GROUP_NAME为core，则可以省略。
* 不同k8s版本以及不同资源是对应不同apiVersion的。
* 可以通过命令 ``kubectl explain $source`` 查看当前版本的资源应该选择何种apiVersion

| 参考：
| https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.11/
| https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html

---------------------
2、kind
---------------------

* 表示资源类型。

---------------------
3、metadata
---------------------

* 表示资源的元数据。
* metadata下还有更多的嵌套数据。

---------------------
4、spec
---------------------

* 定义资源规格，目标期望状态。
* spec下还有更多的嵌套数据。

---------------------
5、status
---------------------

* 只读，显示当前状态。
* status的状态会无限趋近spec定义的目标期望状态。