=============================
argparse
=============================

用argparse来更优雅地处理脚本执行参数


-----------------------
1、功能
-----------------------

**Command-line option and argument parsing library.**

----------------------
2、使用方法
----------------------


.. code-block:: python

    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument('-t', '--type', help="请输入加密类型", type=str, default='str')
    args =  parser.parse_args()

* 先导入模块
* 然后实例化对象
* 接着添加参数规则，支持添加多条规则
* 然后对刚才实例化对象调用parse_args()方法:

*小括号内容，依次为:参数别名1，参数别名2，当输入-h时候回写帮助内容，输入内容被转义后的类型(比如上面是str，那么并不是说不能跟随参数-t 1，此时1会被转义为str类型。而当type=int的时候，如果输入-t cisco，则cisco会因为无法转成int类型而提示用法错误。如果是多个参数的情况下，则是转义list中的每个值)， 默认值。*

打印传入的参数

.. code-block:: python

    args.type

*上面的参数是type，所以直接对象.参数别名就能获取到参数了*

----------------------
3、一个完整的例子
----------------------

.. code-block:: python

    import argparse

    if __name__ == '__main__':
        parser = argparse.ArgumentParser()
        parser.add_argument('-t', '--type', help="请输入加密类型", type=str, default='str')
        parser.add_argument('-c', '--content', help="请输入字符串或路径", type=str, default='huangyisan')

        # 对parser调用parse_args()方法
        args =  parser.parse_args()
        print(args)
        print(args.type)
        print(args.content)

执行结果：

.. code-block:: python

    huangyisan:~ $ python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/arg.py -t kk -c 'ok'
    Namespace(content='ok', type='kk')
    kk
    ok
    huangyisan:~ $ python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/arg.py  -c 'ok'
    Namespace(content='ok', type='str')
    str
    ok

------------------------
4、传入多个参数
------------------------

**如果需要传入多个参数，比如-t a b c d 则添加nargs参数，并且定义为'+'，并且把default修改成list类型：**

.. code-block:: python

    import argparse

    if __name__ == '__main__':
        parser = argparse.ArgumentParser()
        parser.add_argument('-t', '--type', help="请输入加密类型", type=str, default='str')
        parser.add_argument('-c', '--content', help="请输入字符串或路径", type=str, default='huangyisan')
        parser.add_argument('-p', '--port', help='端口', type=int ,default=[80], nargs='+')

        args =  parser.parse_args()
        print(args)
        print(args.type)
        print(args.content)
        print(args.port)

*上面* ``--port`` *就是可以捕获到多个参数, nargs='+',default用数组形式赋值*

执行结果：

.. code-block:: python

    huangyisan:~ $ python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/arg.py  -c 'ok'
    Namespace(content='ok', port=[80], type='str')
    str
    ok
    [80]
    huangyisan:~ $ python3 /Users/huangyisan/Desktop/Python_project/Mage_edu/normal_func/arg.py  -c 'ok' -p 80 8080 90
    Namespace(content='ok', port=[80, 8080, 90], type='str')
    str
    ok
    [80, 8080, 90]