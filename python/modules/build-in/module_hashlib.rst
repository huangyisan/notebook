=============================
hashlib
=============================

-----------
1、功能
-----------
**Secure hash and message digest algorithms.**


-----------------
2、hashlib的方法
-----------------

查看hashilib的方法：

.. code-block:: python

    >>> import hashlib
    >>> dir(hashlib)
    ['__all__', '__builtin_constructor_cache', '__builtins__', '__cached__', '__doc__', '__file__', '__get_builtin_constructor', '__loader__', '__name__', '__package__', '__spec__', '_hashlib', 'algorithms_available', 'algorithms_guaranteed', 'blake2b', 'blake2s', 'md5', 'new', 'pbkdf2_hmac', 'sha1', 'sha224', 'sha256', 'sha384', 'sha3_224', 'sha3_256', 'sha3_384', 'sha3_512', 'sha512', 'shake_128', 'shake_256']
    >>>

--------------------
3、计算字符串md5值
--------------------

* 首先实例化md5

.. code-block:: python

    >>> md5 = hashlib.md5()

*如果要用其他的，比如sha256之类的也就只需要md5=hashlib.sha256() 来实例化md5即可。*

* 使用md5.update(b'string')更新md5，``必须是二进制进行计算``
* 最后用md5.hexdigest()来得到md5值

.. code-block:: python

    >>> md5.update(b'huangyisan')
    >>> md5.hexdigest()
    '1b4ae8b712bd84499feb814954393eb2'

**此时如果想继续算某个字符串的md5，不能直接md5.update(b'string')，因为刚才的计算已经污染了，需要重新实例化md5才可以。**

.. code-block::python

    >>> md5.update('huangdou'.encode())
    >>> md5.hexdigest()
    '5939ab3992bdb6d4db592beff16a95a8'

*上面得到了错误的结果，上面的结果是对huangyisanhuangdou进行了求md5值*

修改后的代码如下：

.. code-block:: python

    # 重新实例化md5
    >>> md5=hashlib.md5()
    >>> md5.update('huangdou'.encode())
    >>> md5.hexdigest()
    '3742e5f15192d17f1d1c57b16187d04f'


------------------------------------------
4、一个能计算文件或字符串的md5函数
------------------------------------------

**要考虑字符串可能是数字，字符，或者是二进制流**

* 可以把这些类型除了二进制的都转换为字符串，然后再都转换成二进制(主要是处理数字的情况)，因为update('')必须是二进制。
* 因为要转换，所以要判断是否是二进制或者非二进制。
* 可以通过``type()``去比较，或者用``isinstance()``去判断。

type()和isinstance()代码示例：

.. code-block:: python

    >>> type(b'huan') == type(b'')
    True
    >>> isinstance('huang',bytes)
    False

**当对文本进行md5的时候，则需要指定读多少大小，然后判断是否读完，同时还需要判断输入的文件是否存在。**

判断文件是否存在,可以用 ``os.path.exists`` 方法。
代码示例：

.. code-block:: python

    >>> os.path.exists('/Users/huangyisan/Desktop/qmtv/front-2.0/vhosts/map.conf')
    True

函数如下：

.. code-block:: python

    import os
    import hashlib

    # 定义size大小
    SIZE = 1024 * 1024 * 1024

    def md5_string(string):
        # 判断string类型
        if not isinstance(string, bytes):
            string = str(string).encode()
        md5 = hashlib.md5()
        md5.update(string)
        return md5.hexdigest()

    def md5_file(path):
        # 判断文件是否存在
        if os.path.exists(path):
            md5 = hashlib.md5()
            fhandler = open(path, 'rb')
            while True:
                cxt = fhandler.read(SIZE)
                # 判断文件是否读完
                if not cxt:
                    break
                md5.update(cxt)

            fhandler.close()
            return md5.hexdigest()
        return '文件不存在'

    if __name__ == '__main__':
        print(md5_string(1))
        print(md5_string('huangyisan'))
        print(md5_string(b'huangdou'))
        print(md5_file('/Users/huangyisan/Desktop/qmtv/front-2.0/vhosts/map.conf'))

执行得到的结果为：

.. code-block:: python

    c4ca4238a0b923820dcc509a6f75849b
    1b4ae8b712bd84499feb814954393eb2
    3742e5f15192d17f1d1c57b16187d04f
    9a571941cba0cd94d62bca883765427f