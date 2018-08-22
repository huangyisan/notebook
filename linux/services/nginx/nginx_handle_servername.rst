===============================
nginx中server_name查询顺序
===============================

-------------------------
1、server_name配置类型
-------------------------

* 确切的域名
* 泛域名  \*.baidu.com
* 正则域名  ``使用“~”开头`` ，后面写正则表达式，比如  ~^(?<user>.+)\.example\.net$

-----------------
2、匹配顺序
-----------------

| 1、精确匹配
| 2、以*开头，最长匹配的泛域名
| 3、以*结尾，最长匹配的泛域名
| 4、对于正则域名，在nginx配置文件中，从上至下，顺序匹配正则域名，匹配到的第一个


-----------------
3、泛域名
-----------------

**对于泛域名来说，\*只能出现在域名开头和结尾中，出现在域名中间，都是非法的。**

*www.\*.xx.com, w\*.xx.com都是非法的。*

有一种特殊的泛域名为  .xxx.com，其不包含\*

------------------
4、正则域名
------------------

| 1、使用~开头进行匹配，一般都写上^和$，来限定域名。
| 2、如果在正则里面存在“{”和"}"，则需要将域名用引号引起来：

.. code-block:: python

    server_name "~^(?<name>\w\d{1,3}+)\.example\.net$";

| 3、正则中可以抓取变量部分，进行后面语法中的使用，比如:

.. code-block:: python

    server {
        server_name   ~^(www\.)?(?<domain>.+)$;

        location / {
            root   /sites/$domain;
        }
    }

*如果配置完 重启，报错如下，则说明系统的PCRE库版本过旧需要更新*

.. code-block:: python

    pcre_compile() failed: unrecognized character after (?< in ...

正则内容捕获，也可以用常用的是用$number来表示：

.. code-block:: python

    server {
        server_name   ~^(www\.)?(.+)$;

        location / {
            root   /sites/$2;
        }
    }

------------------
5、域名配置优化
------------------

* 精确匹配域名，泛域名，正则域名会对应创建三张表和侦听端口关联。
* 先搜索精确匹配表，然后泛域名表，最后搜索正则域名表。
* 泛域名表的查询比精确匹配表查询慢。
* 正则域名表则会依次查询，效率最低。
* 尽可能的采用精确匹配域名，来获得最大化效率

----------------------
6、域名过长的情况
----------------------


| 如果定义了一个过长的域名，则可能需要修改http上下文中``server_names_hash_max_size`` 和 ``server_names_hash_bucket_size`` 两个属性值，否则启动nginx会出现报错。
| 默认的 ``server_names_hash_max_size`` 和 ``server_names_hash_bucket_size`` 的值和环境中cpu cache line size相关，32或者64。

查看cpu cache line size可以用getconf命令：

.. code-block:: python

    [root@Master_132 ~]# getconf -a | grep -i _CACHE_LINESIZE
    LEVEL2_CACHE_LINESIZE              64
    LEVEL3_CACHE_LINESIZE              64

如果一个端口只被一个server块侦听，则nginx不会去尝试并且记录server_name到hash表中。

**但是有个例外。当server_name存在正则捕获的情况，nginx是会去执行正则表达式捕获的。**

.. code-block:: python

    server {
        server_name   ~^(www\.)?(?<domain>.+)$;
        location / {
            root   /sites/$domain;
        }
    }
