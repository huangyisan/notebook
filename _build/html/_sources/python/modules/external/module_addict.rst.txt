=============================
addict
=============================

-----------
1、功能
-----------

**addict模块可以构造嵌套字典，或者解字典。**


------------
2、安装
------------

.. code-block:: python

    pip install addict

--------------------
3、构造一个嵌套字典
--------------------

代码:

.. code-block:: python

    from addict import Dict
    mapping = Dict()
    mapping.a.b.c.d.e = 2
    print(mapping)

* 首先实例化Dict()对象。
* 然后进行嵌套，比如上面是最外层到最内层嵌套a b c d e。
* 最后赋值2，是给最后一个key，也就是e进行赋值为2。

得到的输出为：

.. code-block:: python

    {'a': {'b': {'c': {'d': {'e': 2}}}}}


------------------------
4、根据结构构造字典
------------------------

.. code-block:: python

    body = {
        'query': {
            'filtered': {
                'query': {
                    'match': {'description': 'addictive'}
                },
                'filter': {
                    'term': {'created_by': 'Mats'}
                }
            }
        }
    }

* 捋一捋关系，query嵌套了filtered。
* filtered内的query和filter是平行关系。
* 内层的query包含match；filter包含term。
* match和term又有各自的嵌套字典。

代码如下：

.. code-block:: python

    >>> from addict import Dict
    >>> mapping = Dict()
    # 构造filtered的第一个分支，query
    >>> mapping.query.filtered.query.match.description = 'addictive'
    # 构造filtered的第二个分支，filter
    >>> mapping.query.filtered.filter.term.created_by = 'Mats'

查看mapping输出的结果：

.. code-block:: python

    {'query': {'filtered': {'query': {'match': {'description': 'addictive'}}, 'filter': {'term': {'created_by': 'Mats'}}}}}

------------------------
5、Dict(dict)操作
------------------------

| **可以对一个已经存在的字典进行Dict()操作**
| **但得到的对象的值和原有的字典相等，但并不是同一个内存地址**

代码如下：

.. code-block:: python

    >>> from addict import Dict
    >>> dictt = {'query': {'filtered': {'query': {'match': {'description': 'addictive'}}, 'filter': {'term': {'created_by': 'Mats'}}}}}
    >>> mapping = Dict(dictt)
    >>> mapping
    {'query': {'filtered': {'query': {'match': {'description': 'addictive'}}, 'filter': {'term': {'created_by': 'Mats'}}}}}
    >>> dictt
    {'query': {'filtered': {'query': {'match': {'description': 'addictive'}}, 'filter': {'term': {'created_by': 'Mats'}}}}}

使用==比较值，使用is比较内存地址：

.. code-block:: python

    >>> mapping == dictt
    True
    >>> mapping is dictt
    False

-------------------------
6、解嵌套字典
-------------------------

* **使用** ``object.key`` **的方式**
* **使用** ``dict[key]`` **的方式**

object.key的方式代码如下：

.. code-block:: python

    >>> mapping.query
    {'filtered': {'query': {'match': {'description': 'addictive'}}, 'filter': {'term': {'created_by': 'Mats'}}}}
    >>> mapping.query.filtered
    {'query': {'match': {'description': 'addictive'}}, 'filter': {'term': {'created_by': 'Mats'}}}

* mapping.query，取出query的value
* mapping.query,filtered，取出query嵌套的filtered的value

dict[key]的方式代码如下：

.. code-block:: python

    >>> mapping['query']
    {'filtered': {'query': {'match': {'description': 'addictive'}}, 'filter': {'term': {'created_by': 'Mats'}}}}