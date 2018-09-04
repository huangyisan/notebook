=============================
FAQ
=============================

| 1、如何查看pod中某个container的日志？
.. code-block:: python

    kubectl logs $pod_name $container_name

| 2、如何连接进入pod中某个container？
.. code-block:: python

    kubectl exec -it $pod_name -c $container_name  -- /bin/sh

| 3、查看pod资源标签，以及过滤标签
.. code-block:: python

    kubectl get pods --show-labels
    kubectl get pods -l $labels_string1, $labels_string2 --show-labels
    kubectl get pods -l "$key in ($value1,$value2,$value3)"
    kubectl get pods -l "$key notin ($value1,$value2,$value3)"

| 4、给资源打标签
.. code-block:: python

    kubectl label $resource $resource_name $key=$value
    强行覆盖打标签，--overwrite

| 5、查看资源的annotation注解, 存活探测结果
.. code-block:: python

    kubectl describe $resource $resource_name

