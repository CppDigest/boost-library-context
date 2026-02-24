# #1488 - gcc-8 maybe-uninitialized warning with websocket::stream and `multi_buffer` [Closed]

> Username: djarek  
> Created at: 2019-03-01 19:48:20 UTC  
> Updated at: 2019-03-01 21:19:57 UTC  
> Closed at: 2019-03-01 21:19:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1488  

```gcc.compile.c++ bin.v2/libs/beast/example/websocket/server/async/gcc-8/release/cxxstd-11-iso/debug-symbols-on/threadapi-pthread/threading-multi/visibility-hidden/websocket_server_async.o  
In file included from ./boost/beast/core/buffers_suffix.hpp:144,  
                 from ./boost/beast/core.hpp:24,  
                 from libs/beast/example/websocket/server/async/websocket_server_async.cpp:16:  
./boost/beast/core/impl/buffers_suffix.hpp: In member function ‘void boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::read_op< <template-parameter-2-1>, <template-parameter-2-2> >::operator()(boost::beast::error_code, std::size_t, bool) [with Handler = boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, long unsigned int), std::shared_ptr<session> >; DynamicBuffer = boost::beast::basic_multi_buffer<std::allocator<char> >; NextLayer = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; bool deflateSupported = true]’:  
./boost/beast/core/impl/buffers_suffix.hpp:144:40: warning: ‘*((void*)& mb +8)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
     , begin_(net::buffer_sequence_begin(bs_))  
              ~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~  
In file included from ./boost/beast/websocket/stream.hpp:2739,  
                 from ./boost/beast/websocket.hpp:18,  
                 from libs/beast/example/websocket/server/async/websocket_server_async.cpp:17:  
./boost/beast/websocket/impl/read.hpp:660:47: note: ‘*((void*)& mb +8)’ was declared here  
         boost::optional<mutable_buffers_type> mb;  
```  
Doesn't occur when `flat_buffer` is used.
