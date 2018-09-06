=============================
FAQ
=============================

| 1、如何查看pod中某个container的日志？

.. code-block:: python

    kubectl logs $pod_name $container_name

| 2、如何连接进入pod中某个container？

.. code-block:: python

    kubectl exec -it $pod_name -c $container_name  -- /bin/sh

| 3、查看pod资源标签，以及过滤标签，标签选择器selector的matchExpressions和matchLabels区别

.. code-block:: python

    kubectl get pods --show-labels
    kubectl get pods -l $labels_string1, $labels_string2 --show-labels   # matchLabels的方式
    kubectl get pods -l "$key in ($value1,$value2,$value3)"    # matchExpressions的方式
    kubectl get pods -l "$key notin ($value1,$value2,$value3)"  # matchExpressions的方式

| 4、给资源打标签

.. code-block:: python

    kubectl label $resource $resource_name $key=$value
    强行覆盖打标签，--overwrite

| 5、查看资源的annotation注解, 存活探测结果

.. code-block:: python

    kubectl describe $resource $resource_name

| 6、修改编辑资源，可用于修改副本数量，滚动升级等操作。不是每一种key的value都是可以被修改的

.. code-block:: python

    kubectl edit $resource $resource_name

| 7、查看资源的滚动历史

.. code-block:: python

    kubectl rollout history $resource $resource_name

| 8、对deployment打补丁操作,patch的方式
| kubectl patch deployment $resource_name -p $json_string

.. code-block:: python

    kubectl patch deployment myapp-deploy -p '{"spec":{"replicas":5}}'

| 9、使用set命令替换deployment中资源的镜像

.. code-block:: python

    kubectl set image deployment $deployment_name $image_key=$value

| 10、暂停某个更新中的deployment

.. code-block:: python

    kubectl rollout pause deployment $deployment_name

| 11、监视deployment更新过程

.. code-block:: python

    kubectl rollout status deployment $deployment_name

| 12、继续更新暂停中的deployment

.. code-block:: python

    kubectl rollout resume  deployment $deployment_name

| 13、回滚deployment到上个版本，或回滚到指定版本

.. code-block:: python

    kubectl rollout undo deployment $deployment_name
    kubectl rollout undo deployment $deployment_name  --to-revision=1

| 14、创建和查看namespace

.. code-block:: python

    kubectl create namespace dev
    kubectl get ns

| 15、获取指定名称空间的pod资源信息

.. code-block:: python

    kubectl get pod -n $namespace

| 16、kubectl create和kubectl apply的区别
| create 行为偏向从无到有产生一个资源，apply 行为偏向对一个已经存在的资源进行upate操作。