# #847 - Uninitialized variable warning [Closed]

> Username: djarek  
> Created at: 2017-10-28 17:21:39 UTC  
> Updated at: 2017-12-04 21:20:28 UTC  
> Closed at: 2017-12-02 14:39:15 UTC  
> Url: https://github.com/boostorg/beast/issues/847  

Building release build of examples on ```gcc version 7.0.1 20170407 (experimental) [trunk revision 246759] (Ubuntu 7-20170407-0ubuntu2)```  
produces the following warnings:  
  
```gcc.compile.c++ bin.v2/libs/beast/example/websocket/client/sync/gcc-gnu-7/release/debug-symbols-on/threading-multi/websocket_client_sync.o  
In file included from ./boost/beast/core/buffers_cat.hpp:117:0,  
                 from ./boost/beast/core.hpp:18,  
                 from libs/beast/example/websocket/client/sync/websocket_client_sync.cpp:18:  
./boost/beast/core/impl/buffers_cat.ipp: In function ‘std::size_t boost::asio::buffer_size(const BufferSequence&) [with BufferSequence = boost::beast::detail::buffers_ref<boost::beast::buffers_cat_view<boost::asio::const_buffer, boost::asio::const_buffer, boost::asio::const_buffer, boost::beast::http::basic_fields<std::allocator<char> >::reader::field_range, boost::beast::http::chunk_crlf> >]’:  
./boost/beast/core/impl/buffers_cat.ipp:255:29: warning: ‘*((void*)& iter +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
             return *iter<I>();  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-28 17:38:03 UTC  
> Url: https://github.com/boostorg/beast/issues/847#issuecomment-340207591  

False positive, unsure how to fix

---

## Comment 2

> Username: pierreN  
> Created at: 2017-10-31 06:54:04 UTC  
> Updated at: 2017-10-31 06:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/847#issuecomment-340676172  

gcc 7.2.0 puts the warning also, with beast version 130 (last commit: caf144388778e)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-31 13:05:12 UTC  
> Url: https://github.com/boostorg/beast/issues/847#issuecomment-340755858  

Is it just that one .cpp file?

---

## Comment 4

> Username: pierreN  
> Created at: 2017-10-31 17:31:44 UTC  
> Updated at: 2017-10-31 17:53:27 UTC  
> Url: https://github.com/boostorg/beast/issues/847#issuecomment-340840568  

The full warning while compiling my project is:  
  
  
```  
[  6%] Building CXX object [///]  
In file included from /opt/boost/boost/beast/core/buffers_cat.hpp:117:0,  
                 from /opt/boost/boost/beast/core.hpp:18,  
                 from ~/project/http_downloader.h:10,  
/opt/boost/boost/beast/core/impl/buffers_cat.ipp: In function ‘std::size_t boost::asio::buffer_size(const BufferSequence&) [with BufferSequence = boost::beast::detail::buffers_ref<boost::beast::buffers_cat_view<boost::asio::const_buffer, boost::asio::const_buffer, boost::a  
sio::const_buffer, boost::beast::http::basic_fields<std::allocator<char> >::reader::field_range, boost::beast::http::chunk_crlf> >]’:  
/opt/boost/boost/beast/core/impl/buffers_cat.ipp:255:29: warning: ‘*((void*)& iter +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
             return *iter<I>();  
                             ^  
In file included from /opt/boost/boost/asio/detail/reactive_socket_service.hpp:22:0,  
                 from /opt/boost/boost/asio/basic_socket.hpp:40,  
                 from /opt/boost/boost/asio/basic_socket_acceptor.hpp:20,  
                 from /opt/boost/boost/asio/ip/tcp.hpp:19,  
                 from ~/project/http_downloader.h:8,  
/opt/boost/boost/asio/buffer.hpp:550:12: note: ‘*((void*)& iter +16)’ was declared here  
   Iterator iter = begin;  
            ^~~~  
[  9%] Building CXX object [///]  
In file included from /opt/boost/boost/asio/detail/reactive_socket_service.hpp:22:0,  
                 from /opt/boost/boost/asio/basic_socket.hpp:40,  
                 from /opt/boost/boost/asio/basic_socket_acceptor.hpp:20,  
                 from /opt/boost/boost/asio/ip/tcp.hpp:19,  
                 from ~/project/http_downloader.h:8,  
/opt/boost/boost/asio/buffer.hpp: In function ‘std::size_t boost::asio::buffer_size(const BufferSequence&) [with BufferSequence = boost::beast::buffers_suffix<boost::beast::buffers_cat_view<boost::beast::http::detail::chunk_size, boost::asio::const_buffer, boost::beast::http::chunk_crlf, boost::asio::const_buffer, boost::beast::http::chunk_crlf> >]’:  
/opt/boost/boost/asio/buffer.hpp:550:12: warning: ‘*((void*)& iter +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
   Iterator iter = begin;  
            ^~~~  
In file included from /opt/boost/boost/beast/core/buffers_cat.hpp:117:0,  
                 from /opt/boost/boost/beast/core.hpp:18,  
                 from ~/project/http_downloader.h:10,  
/opt/boost/boost/beast/core/impl/buffers_cat.ipp: In function ‘std::size_t boost::asio::buffer_size(const BufferSequence&) [with BufferSequence = boost::beast::detail::buffers_ref<boost::beast::buffers_cat_view<boost::asio::const_buffer, boost::asio::const_buffer, boost::asio::const_buffer, boost::beast::http::basic_fields<std::allocator<char> >::reader::field_range, boost::beast::http::chunk_crlf> >]’:  
/opt/boost/boost/beast/core/impl/buffers_cat.ipp:255:29: warning: ‘*((void*)& iter +16)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
             return *iter<I>();  
                             ^  
In file included from /opt/boost/boost/asio/detail/reactive_socket_service.hpp:22:0,  
                 from /opt/boost/boost/asio/basic_socket.hpp:40,  
                 from /opt/boost/boost/asio/basic_socket_acceptor.hpp:20,  
                 from /opt/boost/boost/asio/ip/tcp.hpp:19,  
                 from ~/project/http_downloader.h:8,  
/opt/boost/boost/asio/buffer.hpp:550:12: note: ‘*((void*)& iter +16)’ was declared here  
   Iterator iter = begin;  
```  
  
and   
  
```  
$ head -10 ../http_downloader.h  
#ifndef __HTTP_H__  
# define __HTTP_H__  
  
  
//#pragma GCC diagnostic push  
//#pragma GCC diagnostic ignored "-Wmaybe-uninitialized"  
# include <boost/asio/io_service.hpp>  
# include <boost/asio/ip/tcp.hpp>  
# include <boost/asio/ssl.hpp>  
# include <boost/beast/core.hpp>  
```  
  
  
Note that disabling the warning in gcc for asio/beast is done via:  
  
```  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wmaybe-uninitialized"  
# include <boost/asio/io_service.hpp>  
# include <boost/asio/ip/tcp.hpp>  
# include <boost/asio/ssl.hpp>  
# include <boost/beast/core.hpp>  
# include <boost/beast/http.hpp>  
# include <boost/beast/version.hpp>  
# include <boost/asio.hpp>  
# include <boost/asio/ssl.hpp>  
#pragma GCC diagnostic pop  
```  
  
which removes the warnings, but i guess there is better workarounds.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-31 20:59:15 UTC  
> Url: https://github.com/boostorg/beast/issues/847#issuecomment-340904900  

This looks like a bug in gcc, someone should open an issue

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-12-02 14:39:15 UTC  
> Url: https://github.com/boostorg/beast/issues/847#issuecomment-348696207  

I believe this is fixed with the refactoring of `buffers_cat_view` in https://github.com/boostorg/beast/commit/b59d0a5cd5219375cf77850a449639f5a3e35a9f

---

## Comment 7

> Username: pierreN  
> Created at: 2017-12-04 21:20:27 UTC  
> Url: https://github.com/boostorg/beast/issues/847#issuecomment-349109411  

I confirm this is no longer an issue. Thanks!
