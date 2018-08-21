=============================
os
=============================

os模块的一些常见的用法

-----------------------------
1、功能
-----------------------------

**Miscellaneous operating system interfaces.**

---------------------
2、os.system()
---------------------

| 调用系统命令
| 用法 ``os.system('command')``


^^^^^^^^^^^^^^^^^^^^^
1.1、调用系统命令
^^^^^^^^^^^^^^^^^^^^^


一个简单的例子，**最后一行的0为返回值：**

.. code-block:: python

    >>> os.system('ping www.baidu.com -c 10')
    PING www.a.shifen.com (180.97.33.107): 56 data bytes
    64 bytes from 180.97.33.107: icmp_seq=0 ttl=55 time=11.707 ms
    64 bytes from 180.97.33.107: icmp_seq=1 ttl=55 time=8.147 ms
    64 bytes from 180.97.33.107: icmp_seq=2 ttl=55 time=27.679 ms
    64 bytes from 180.97.33.107: icmp_seq=3 ttl=55 time=9.168 ms
    64 bytes from 180.97.33.107: icmp_seq=4 ttl=55 time=10.095 ms
    64 bytes from 180.97.33.107: icmp_seq=5 ttl=55 time=15.279 ms
    64 bytes from 180.97.33.107: icmp_seq=6 ttl=55 time=9.284 ms
    64 bytes from 180.97.33.107: icmp_seq=7 ttl=55 time=8.868 ms
    64 bytes from 180.97.33.107: icmp_seq=8 ttl=55 time=8.779 ms
    64 bytes from 180.97.33.107: icmp_seq=9 ttl=55 time=8.844 ms

    --- www.a.shifen.com ping statistics ---
    10 packets transmitted, 10 packets received, 0.0% packet loss
    round-trip min/avg/max/stddev = 8.147/11.785/27.679/5.659 ms
    0

^^^^^^^^^^^^^^^^^^^^^
1.2、获取执行返回值
^^^^^^^^^^^^^^^^^^^^^

对命令赋值则得到返回值：

.. code-block:: python

    >>> s = os.system('ping www.baidu.com -c 1')
    PING www.a.shifen.com (180.97.33.108): 56 data bytes
    64 bytes from 180.97.33.108: icmp_seq=0 ttl=55 time=10.432 ms

    --- www.a.shifen.com ping statistics ---
    1 packets transmitted, 1 packets received, 0.0% packet loss
    round-trip min/avg/max/stddev = 10.432/10.432/10.432/0.000 ms
    >>> s
    0

*执行成功，则返回值为0*

使用 ``sys.exit(xxx)`` 自定义返回值：

.. code-block:: python

    import sys
    if __name__ == '__main__':
        sys.exit(2)

^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
1.3、python执行返回值和shell不同的情况
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

python执行返回值为512

.. code-block:: python

    >>> os.system('python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/exit_code.py')
    512

shell中执行得到的返回值为2

.. code-block:: python

    huangyisan\:~ $ python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/exit_code.py
    huangyisan\:~ $ echo $?
    2

**原因为，c的int占4个字节，所以"2"是"512"右移8位得到的，"512"是"2"左移8位得到的。**

.. code-block:: python

    >>> 512>>8
    2
    >>> 2<<8
    512

---------------------
3、os.popen()
---------------------

| 获取执行内容

.. code-block:: python

    >>> f = os.popen('ping baidu.com -c 1')
    >>> f.read()
    'PING baidu.com (220.181.57.216): 56 data bytes\n64 bytes from 220.181.57.216: icmp_seq=0 ttl=56 time=30.260 ms\n\n--- baidu.com ping statistics ---\n1 packets transmitted, 1 packets received, 0.0% packet loss\nround-trip min/avg/max/stddev = 30.260/30.260/30.260/0.000 ms\n'
    >>> f.close()

* **先os.popen()实例化，然后对对象read()操作。**
* **最后对对象进行close()关闭操作。**

可以对内容进行遍历：

.. code-block:: python

    >>> f = os.popen('ping baidu.com -c 1')
    >>> for i in f:
    ...     print(i)
    ...
    PING baidu.com (220.181.57.216): 56 data bytes

    64 bytes from 220.181.57.216: icmp_seq=0 ttl=56 time=31.261 ms



    --- baidu.com ping statistics ---

    1 packets transmitted, 1 packets received, 0.0% packet loss

    round-trip min/avg/max/stddev = 31.261/31.261/31.261/0.000 ms
    >>> f.close()
