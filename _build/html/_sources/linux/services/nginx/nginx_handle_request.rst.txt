===============================
nginx如何处理一个请求
===============================

---------------------------------
1、请求原则
---------------------------------

| 1、当一个请求给nginx的时候，nginx通过检查请求头中"Host"字段，来判断把请求递交给哪个域名（如果请求的端口对应存在多个域名的情况下）。
| 2、如果没有一个域名匹配，则递交给按配置文件从上至下第一个server，如果有server被指定 ``default_server`` ,则被这个server所处理。

.. code-block:: python

    server {
       listen 80;
       server_name example.huangyisan.com;
       location / {
       return 200 example.huangyisan.com;
       }
    }

    server {
       listen 80 default_server;
       server_name example.huangyisan.net;
       location / {
       return 200 example.huangyisan.net;
       }
    }

    server {
       listen 80;
       server_name example.huangyisan.cn;
       location / {
       return 200 example.huangyisan.cn;
       }
    }

**如果请求expample.huangyisan.cc则会被 标记了 default_server关键字的 example.huangyisan.net响应。**


----------------------------
2、拒绝未定义的server_name
----------------------------

如果要拒绝其他未定义的server_name，则可以在nginx.conf顺序里面，第一个写:

.. code-block:: python

    server {
        listen 80;
        location / {
            deny all;
        }
    }

------------------------------------
3、ip和server_name共存的情况
------------------------------------

**当ip和server_name同时存在，且ip多个的时候，首先nginx检测ip，然后在检测header中的Host对应的值去匹配server_name，从而决定分配请求给哪个server。**

.. code-block:: python

    server {
       listen 192.168.1.1:80;
       server_name example.org www.example.org;
       ...
    }

    server {
       listen 192.168.1.1:80 default_server;
       server_name example.net www.example.net;
       ...
    }

    server {
       listen 192.168.1.2:80 default_server;
       server_name example.com www.example.com;
       ...
    }

*如上配置，如果访问了不存在的server_name，那么ip如果是请求192.168.1.1的则递交给第二个server。*