=============================
logrotate
=============================

-----------------------
1、两种切割模式
-----------------------

^^^^^^^^^^^^^^^^^^^
1.1、传统模式
^^^^^^^^^^^^^^^^^^^

| 1、首先重命名当前进程正在输出的日志文件名称，因为进程是根据 ``inode`` 号来确定往哪个日志文件输出，更改日志文件名称并不会影响inode号，所以这步行为中，进程依旧会往修改了名称的日志文件内输出日志。
| 2、进行新的日志创建，创建新的日志名称和老旧的日志名称一样，但是因为是新建的。所以inode号不一样。此时进程日志还是依旧输出到老的被重命名了的日志文件里面。
| 3、对进程发起信号通知，让其重新写日志，比如nginx为 ``kill -user1 pid`` 让其重载配置文件，实现平滑重启，然后来写入到新建的日志里面。

* nginx可能会对不同location定义不同的日志文件。所以一天的日志会有多个不同的日志文件，因为每滚动一次都会进行kill -user1 pid的信号发送，所以需要配合 ``shardscripts`` 参数，让程序把所有日志都重命名了以后，只发送一次kill信号。

^^^^^^^^^^^^^^^^^^^^^^^
1.2、copytruncate模式
^^^^^^^^^^^^^^^^^^^^^^^

| 1、copy当前日志文件，重命名为新文件，这样进程还是往老的文件写入。
| 2、然后logrotate对老文件进行truncate，对老文件进行清空。这样就完成了一次日志切割。

* 这种日志切割不需要对进程发起重载信号。
* 但有个风险，因为会对日志文件进行copy，如果系统文件巨大，那么系统可用空间会突然暴增。


--------------------------
2、控制执行
--------------------------

logrotate内容一般不需要写入crontab里面。因为其通过 ``cron.daily`` 来控制执行。

.. code-block:: python

    $cat /etc/cron.daily/logrotate

    #!/bin/sh

    /usr/sbin/logrotate /etc/logrotate.conf
    EXITVALUE=$?
    if [ $EXITVALUE != 0 ]; then
        /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
    fi
    exit 0


**默认情况下,logrotate一般都在凌晨三点左右执行，这是由** ``anacrontab `` **来控制的。**

.. code-block:: python

    $cat /etc/anacrontab

    # /etc/anacrontab: configuration file for anacron

    # See anacron(8) and anacrontab(5) for details.

    SHELL=/bin/sh
    PATH=/sbin:/bin:/usr/sbin:/usr/bin
    MAILTO=root
    # the maximal random delay added to the base delay of the jobs
    RANDOM_DELAY=45
    # the jobs will be started during the following hours only
    START_HOURS_RANGE=3-22

* 由 ``RANDOM_DELAY`` 和 ``START_HOURS_RANGE`` 两个参数共同决定。


------------------------
3、语法测试
------------------------

可以对logrotate.d下面的独立配置文件进行语法测试

.. code-block:: python

    logrotate -d $file

-------------------------
4、nginx日志切割配置举例
-------------------------

.. code-block:: python

    /app/log/nginx/*.log {
        daily
        rotate 5
        missingok
        notifempty
        compress
        sharedscripts
        postrotate
            /bin/kill -USR1 $(cat /var/run/nginx.pid 2>/dev/null) 2>/dev/null || :
        endscript
    }