=============================
一. ABC抽象类
=============================


---------------
1、抽象类写法
---------------
**如果要创建一个ABC的抽象类，则需要继承ABC，或者指定** ``metaclass=ABCMeta``

使用继承ABC的写法：

.. code-block:: python

    from abc import ABC

    class MyABC(ABC):
        pass

使用指定metaclass的写法：

.. code-block:: python

    from abc import ABCMeta

    class MyABC(metaclass=ABCMeta):
        pass

-----------------
2、抽象类注册
-----------------

**把一个对象注册为抽象类，使用** ``class.register()``

.. code-block:: python

    from abc import ABC

    class MyABC(ABC):
        pass

    MyABC.register(tuple)

    assert issubclass(tuple, MyABC)
    assert isinstance((), MyABC)

| *MyABC为抽象类，使用MyABC.register()方式，将tuple注册给了抽象类。*
| *所以用issubclass和isinstance验证为true。*

-----------------
3、自定义子类
-----------------

| **使用** ``__subclasshook__`` **,自定义行为来匹配是否属于子类。**
| ``__subclasshook__`` 返回值有 ``True``,``False``,``NotImplemented``。
| ``True`` 则该类属于抽象类的subclass和instance。
| ``False`` 则不属于subclass或instance。
| ``NotImplemented`` 则根据其他方法继续检查是否符合。

一个__subclasshook__的例子：

.. code-block:: python

    from abc import ABC, abstractmethod
    class Foo:
        def __getitem__(self, index):
            ...
        def __len__(self):
            ...
        def get_iterator(self):
            return iter(self)

    class MyIterable(ABC):

        @abstractmethod
        def __iter__(self):
            while False:
                yield None

        def get_iterator(self):
            return self.__iter__()

        @classmethod
        def __subclasshook__(cls, C):
            if cls is MyIterable:
                if any("__iter__" in B.__dict__ for B in C.__mro__):
                    return True
            return NotImplemented

    print(isinstance(Foo(), MyIterable))
    MyIterable.register(Foo)
    print(isinstance(Foo(), MyIterable))

*上述代码，MyIterable为抽象类，里面定义了__subclasshook__方法，判断的依据是，被企望查询的类(代码中被企望查询的类为Foo)，其父类以及所有继承类中的属性和方法时候包含__iter__，如果包含，则返回True，也就是属于，反之返回NotImplemented，进行其他方式查询，比如看Foo是否是继承了MyIterable类。*

*因为Foo继承object基类，而且其方法和属性都不存在__iter__，同时，Foo也没有继承自MyIterable，所以第一条打印的内容为False。*

*随后，通过MyIterable.register()的方法注册了Foo这个类。所以Foo也被认定为MyIterable的子类，所以最后一条打印为True。*

修改一下代码，给Foo增加__iter__方法：

.. code-block:: python

    from abc import ABC, abstractmethod
    class Foo:
        def __getitem__(self, index):
            ...
        def __len__(self):
            ...
        def __iter__(self):
            ...
        def get_iterator(self):
            return iter(self)

    class MyIterable(ABC):

        @abstractmethod
        def __iter__(self):
            while False:
                yield None

        def get_iterator(self):
            return self.__iter__()

        @classmethod
        def __subclasshook__(cls, C):
            if cls is MyIterable:
                if any("__iter__" in B.__dict__ for B in C.__mro__):
                    return True
            return NotImplemented
    print(isinstance(Foo(), MyIterable))

*此时，打印出来的结果为True。*

------------------
4、抽象类实例化
------------------

| **抽象类无法直接被实例化，需要通过继承的方法进行实例化。**
| **若抽象类存在** ``abstractmethod`` **装饰器的方法，在继承的子类中必须存在，否则实例化子类会失败。**

抽象类实例化例子：

.. code-block:: python

    from abc import ABCMeta, abstractmethod
    class A(metaclass=ABCMeta):
        def my_normal_method(self):
            pass

        @abstractmethod
        def my_abstract_method(self):
            pass

    class B(A):
        pass

    class C(A):
        def my_abstract_method(self):
            pass

    B()
    C()

执行失败输出内容：

.. code-block:: python

    TypeError: Can't instantiate abstract class B with abstract methods my_abstract_method

| *此时如果执行，因为B类继承了抽象A类，但B中没有实现A中存在装饰器abstractmethod的my_abstract_method方法，所以报错了。*
| *C类因为定义了* ``my_abstract_method`` *，所以实例化的时候不会报错。*

----------------------------
5、抽象类结合常见装饰器
----------------------------

**只需要在装饰器下面添加** ``@abstractmethod`` **即可**

@classmethod

.. code-block:: python

    class C(ABC):
        @classmethod
        @abstractmethod
        def my_abstract_classmethod(cls, ...):
            ...

@staticmethod

.. code-block:: python

    class C(ABC):
        @staticmethod
        @abstractmethod
        def my_abstract_staticmethod(...):
            ...

@property

.. code-block:: python

    class C(ABC):
    @property
    @abstractmethod
    def my_abstract_property(self):
        ...

@类名.setter

.. code-block:: python

    class C(ABC):
    @x.setter
    @abstractmethod
    def x(self, val):
        ...

-----------------------------
6、 抽象类cache_token()方法
-----------------------------

**token定义了当前针对虚拟接口的抽象基类的cache版本。**
**每次进行register()操作都会刷新token。**

一个简单的例子：

.. code-block:: python

    import abc
    class A(metaclass=abc.ABCMeta):
        @abc.abstractmethod
        def walk(self):
            pass
        def get_token(self):
            return abc.get_cache_token()

    class B(A):
        def walk(self):
            pass

    class C(object):
        pass

    print(B().get_token())

    A.register(C)

    print(B().get_token())

输出结果为:

.. code-block:: python

    36
    37

*输出第一次为36，第二次因为调用了register方法，所以变成了37*