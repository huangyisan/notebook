=============================
hasattr getattr setattr
=============================

----------------------
1、hasattr
----------------------

^^^^^^^^^^^^^^^^
1.1、用途
^^^^^^^^^^^^^^^^

**hasattr用于检测实例是否有某种属性，或者某种方法，如果有则返回True，反之则返回False。**

用法：

.. code-block:: python

    hasattr(obj, "attribute")

^^^^^^^^^^^^^^^^^^^^^
1.2、代码例子
^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    class test(object):
        # name = 'huangyisan'
        def __init__(self):
            self.name = 'huangyisan'

        def get_name(self):
            return self.name

    man = test()
    print(hasattr(man, "name"))
    print(hasattr(man, "get_name"))
    print(hasattr(man, "get_age"))

输出结果:

.. code-block:: python

    True
    True
    False

----------------------
2、getattr
----------------------

^^^^^^^^^^^^^^^^
2.1、用途
^^^^^^^^^^^^^^^^

**用于获取对象属性的值，如果是方法，获取的是内存地址，加上()可以执行该函数。没有则抛出异常，可以设定默认值。**

用法：

.. code-block:: python

    getattr(obj, "attribute", "default value")

^^^^^^^^^^^^^^^^
2.1、代码例子
^^^^^^^^^^^^^^^^

.. code-block:: python

    class test(object):
        # name = 'huangyisan'
        def __init__(self):
            self.name = 'huangyisan'

        def get_name(self):
            return self.name

    man = test()
    print(getattr(man, "name"))
    print(getattr(man, "get_name"))
    print(getattr(man, "get_age",'1'))
    print(getattr(man, "get_name")())   #执行该方法

输出结果为：

.. code-block:: python

    huangyisan
    <bound method test.get_name of <__main__.test object at 0x7f7188f38630>>
    1
    huangyisan


---------------------
3、setattr
---------------------

^^^^^^^^^^^^^^^^^^^^^^
3.1、用途
^^^^^^^^^^^^^^^^^^^^^^

**给对象的属性进行赋值，如果不存在属性，则创建属性后赋值。**

用法：

.. code-block:: python

    setattr(obj,"attribute", "set_value")

^^^^^^^^^^^^^^^^^^^^^^^^^
3.2、代码例子
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    class test(object):
        # name = 'huangyisan'
        def __init__(self):
            self.name = 'huangyisan'

        def get_name(self):
            return self.name

    man = test()
    setattr(man, "name", 'huangyi')
    setattr(man, "age", 27)
    print(man.name)
    print(man.age)

输出结果为：

.. code-block:: python

    huangyi
    27

--------------------------
4、setattr和getattr结合
--------------------------

*getattr和setattr可以结合使用，判断某对象的属性是否存在，存在则取值，反之则创建该属性并且赋值。*

.. code-block:: python

    # age属性不存在时，设置该属性
    getattr(man, "age", setattr(man, "age", "27"))