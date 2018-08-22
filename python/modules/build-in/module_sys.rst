=============================
sys
=============================

sys模块的一些常见用法

-----------------------------
1、功能
-----------------------------

**Access system-specific parameters and functions.**

--------------------------
2、sys.argv()
--------------------------

**sys.argv()可以获取到当前执行脚本以及跟随的执行参数，参数之间的由空格来分割。**

代码如下:

.. code-block:: python

    import sys
    if __name__ == '__main__':
        print(sys.argv)
        sys.exit(0)

执行得到的结果：

.. code-block:: python

    huangyisan:~ $ python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/exit_code.py 1 2 3
    ['/Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/exit_code.py', '1', '2', '3']

* 输出结果打印出了
* 第"0"参数,脚本全路径
* 第"1"参数,1
* 第"2"参数,2
* 第"3"参数,3

^^^^^^^^^^^^^^^^^^^^^^
1.1、获取指定参数
^^^^^^^^^^^^^^^^^^^^^^

**sys.argv[0]则表示脚本全路径，sys.argv[1]则表示第一个参数，后面以此类推。**

代码如下：

.. code-block::python

    import sys
    if __name__ == '__main__':
        print(sys.argv[0])
        print(sys.argv[1])
        sys.exit(0)

执行输出结果：

.. code-block:: python

    huangyisan:~ $ python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/exit_code.py 1 2 3
    /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/exit_code.py
    1