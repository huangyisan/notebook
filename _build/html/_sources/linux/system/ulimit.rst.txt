=============================
浅谈ulimit
=============================

| 参考文档
| http://www.jianshu.com/p/23ee9db2a620
| http://blog.csdn.net/SuperChanon/article/details/13303705
| http://danielzhou82.github.io/blog/2014/09/30/on-the-confusing-ulimit/
| https://serverfault.com/questions/265155/soft-limit-vs-hard-limit

**centos 7**

-----------------------
limits.conf文件
-----------------------
* 该文件对当前登陆shell的用户有效。
* limits.conf文件实际是Linux PAM（插入式认证模块，Pluggable Authentication Modules）中 pam_limits.so 的配置文件，突破系统的默认限制，对系统访问资源有一定保护作用。
* 确定limits.conf文件生效的前提是，确保pam_limits.so 文件被加入到启动文件中。查看 ``/etc/pam.d/login`` 文件中存在 ``session required /lib/security/pam_limits.so``。
* limits.conf是 ``pam_limits.so`` 的配置文件，然后/etc/pam.d/下的应用程序调用pam_***.so模块。譬如说，当用户访问服务器，服务程序将请求发送到PAM模块，PAM模块根据服务名称在/etc/pam.d目录下选择一个对应的服务文件，然后根据服务文件的内容选择具体的PAM模块进行处理。

----------------------
限制文件打开数
----------------------

一般在limits.conf文件中添加如下代码：

.. code-block:: python

    * soft nofile 65535
    * hard nofile 65535

**所有用户的soft limit和hard limit都为65535**

* 这个是 ``单个进程`` 能打开的最大文件数，而不是当前用户所有进程能打开的进程数总和。
* limits.conf中 ``sort limit`` 永远不可以大于 ``hard limit`` 。
* 对于非root用户，不允许更改硬限制或增加soft limit，即非特权用户唯一可以做的就是降低sort limit。
* 子进程会继承父进程的文件打开数量。
* hard limit制约了soft limit， ``soft limit`` 为真正生效的参数。

所有进程打开的文件描述符为：

.. code-block:: python

    $ cat /proc/sys/fs/file-max

    1620050

* 所有进程打开的文件描述符数不能超过 ``/proc/sys/fs/file-max`` 。

重启服务后，相关配置会失效，可以将执行修改的命令写入到/etc/rc.local中：

.. code-block:: python

    echo "ulimit -SHn 102400" >> /etc/rc.local

-----------------------
限制用户最大线程数
-----------------------

最大线程数配置文件为:

.. code-block:: python

    $ cat /etc/security/limits.d/90-nproc.conf

    *          soft    nproc     1024
    root       soft    nproc     unlimited

* 除了root用户没有限制，其他用户默认限制为最大线程1024个。
* 查看某个用户当前使用线程数量为 ``ps -eLf | grep $USER | wc -l`` 。
* nofile的hard limit不能超过 ``/proc/sys/fs/nr_open``。
* 修改后立即生效。

查看当前系统使用的打开文件描述符数

  .. code-block:: python

    $cat /proc/sys/fs/file-nr

    5664        0        186405

其中第一个数表示当前系统已分配使用的打开文件描述符数，第二个数为分配后已释放的（目前已不再使用），第三个数等于file-max。
