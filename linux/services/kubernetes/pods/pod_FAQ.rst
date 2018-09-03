=============================
Pod FAQ
=============================

| 1、如何查看pod中某个container的日志？
| kubectl logs $pod_name $container_name

| 2、如何连接进入pod中某个container？
| kubectl exec -it $pod_name -c $container_name  -- /bin/sh
