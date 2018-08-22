===============================
nginx中的gzip
===============================

----------------------
1、gzip用法
----------------------

| 参考官方文档
| http://nginx.org/en/docs/http/ngx_http_gzip_module.html

--------------------------
2、gzip简单配置
--------------------------

.. code-block:: python

    gzip  on;
    gzip_static always;
    gzip_disable "msie6";
    # gzip_static on;
    gzip_proxied any;
    gzip_min_length 1;
    gzip_comp_level 2;
    gzip_types  text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_buffers 64 4k;
    #gzip_http_version 1.0;
    gzip_vary on;

* 有时候没有压缩可能是 ``gzip_types`` 类型没写引起的。

* 一些jpg或者视频等内容无需压缩，因为其本身就已经被压缩过一次了。

-----------------
3、gzip的表现
-----------------

| gzip压缩后，在response header中有个头部, ``Content-Encoding: gzip``
| *浏览器调试模式中，这个头部有极大的可能当存在* ``Transfer-Encoding:chunked`` *的时候会不出现。*

.. image:: ./images/gzip_response_header.png

| 可以使用wireshark抓包进行确定是否进行了gzip

.. image:: ./images/gzip_wireshark.png


---------------------
4、源码判断gzip条件
---------------------

在nginx源代码里面判断``不进行``压缩的条件是：

.. code-block:: python

    if (!conf->enable
        || (r->headers_out.status != NGX_HTTP_OK
            && r->headers_out.status != NGX_HTTP_FORBIDDEN
            && r->headers_out.status != NGX_HTTP_NOT_FOUND)
        || (r->headers_out.content_encoding
            && r->headers_out.content_encoding->value.len)
        || (r->headers_out.content_length_n != -1
            && r->headers_out.content_length_n < conf->min_length)
        || ngx_http_test_content_type(r, &conf->types) == NULL
        || r->header_only)

* config文件中未开启gzip     gzip on
* 响应头状态码非200 非 403  非404 等其他状态码。
* content_encoding   这个条件未知。
* content_length小于配置文件中gzip_min_length。
* content-type不在config配置文件中 gzip_types。


---------------------------------------
5、和transfer-encoding:chunked的联系
---------------------------------------

虽然transfer-encoding：chunked传输会进行分块传输，从而在response header头部看不到content-type，但并不表示没有，在nginx源代码中开启debug，添加一些信息，则可以看到content-type：

.. code-block:: python

    if (!conf->enable
        || (r->headers_out.status != NGX_HTTP_OK
            && r->headers_out.status != NGX_HTTP_FORBIDDEN
            && r->headers_out.status != NGX_HTTP_NOT_FOUND)
        || (r->headers_out.content_encoding
            && r->headers_out.content_encoding->value.len)
        || (r->headers_out.content_length_n != -1
            && r->headers_out.content_length_n < conf->min_length)
        || ngx_http_test_content_type(r, &conf->types) == NULL
        || r->header_only)

    {
        ngx_log_debug1(NGX_LOG_DEBUG_HTTP, r->connection->log, 0,"huangyisan not ok headers_out.status:%d",r->headers_out.status);
        ngx_log_debug1(NGX_LOG_DEBUG_HTTP, r->connection->log, 0,"huangyisan not ok headers_out.content_encoding:%d",r->headers_out.content_encoding);
        ngx_log_debug1(NGX_LOG_DEBUG_HTTP, r->connection->log, 0,"huangyisan not ok headers_out.content_length_n:%d",r->headers_out.content_length_n);
        return ngx_http_next_header_filter(r);
    }
    else {
        ngx_log_debug1(NGX_LOG_DEBUG_HTTP, r->connection->log, 0,"huangyisan ok headers_out.status:%d",r->headers_out.status);
        ngx_log_debug1(NGX_LOG_DEBUG_HTTP, r->connection->log, 0,"huangyisan ok headers_out.content_encoding:%d",r->headers_out.content_encoding);
        ngx_log_debug1(NGX_LOG_DEBUG_HTTP, r->connection->log, 0,"huangyisan ok headers_out.content_length_n:%d",r->headers_out.content_length_n);
    }

To:

:doc:`My document about chunked <../../../../protocol/http/Transfer-Encoding_chunked>`