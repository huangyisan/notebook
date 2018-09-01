=============================
services
=============================

Pod因为存在生命周期，所以其时刻都可能生成或者销毁，pod的ip也会随之发生改变。此时frontend无法通过固定的ip和port直接访问pod,需要依赖service做请求调度，调度给后端的一组pod。

.. toctree::
   :maxdepth: 1

   service