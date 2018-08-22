=============================
__slots__
=============================

@property和@funcname.setter两个装饰器可以讲实例对象的属性和实例对象的方法进行绑定。

-------------
1、功能
-------------

* 实例对象的哪些属性可以进行绑定，只有存在于__slots__元组中的属性才可以被绑定。
* 只有在元组中的属性才可以被定义。

---------------------
2、对对象属性进行限定
---------------------

.. code-block:: python

    class Person(object):

        # 限定Person对象只能绑定_name, _age和_gender属性
        __slots__ = ('_name', '_age', '_gender')

        def __init__(self, name, age):
            self._name = name
            self._age = age

        @property
        def name(self):
            return self._name

        @property
        def age(self):
            return self._age

        @age.setter
        def age(self, age):
            self._age = age

        def play(self):
            if self._age <= 16:
                print('%s正在玩飞行棋.' % self._name)
            else:
                print('%s正在玩斗地主.' % self._name)


    def main():
        person = Person('王大锤', 22)
        person.play()
        person._gender = '男'
        # AttributeError: 'Person' object has no attribute '_is_gay'

* __slots__的元组中的属性，未必是已经在类中存在的属性。
* 上述例子中，__slot__中绑定了 ``_name`` ``_age`` ``_gender``。则这三者才可以进行绑定或者赋值处理。
* ``_is_gay`` 因为不在元组中，所以当进行赋值操作的时候就报错了。


