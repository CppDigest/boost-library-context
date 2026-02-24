# #1796 - Compiling boost failed with error: ‘begin’ is not a member of ‘boost’ [Closed]

> Username: BloodmageThalnos  
> Created at: 2019-12-31 08:34:59 UTC  
> Updated at: 2020-01-03 06:05:47 UTC  
> Closed at: 2020-01-03 06:05:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1796  

Beast version: 1.72.0  
Compiler: GCC 4.8.5 on CentOS  
  
I just included these files in my code:  
```  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/spawn.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
```  
And the compiler throws lots of errors. This is the first 3 of them:  
```  
Compiling src/WebsocketManager.cpp ==> obj/WebsocketManager.o  
g++ -std=c++11 -O3 -c -o obj/WebsocketManager.o -I./src -I./include -I/usr/boost_1_72_0 src/WebsocketManager.cpp  
In file included from /usr/boost_1_72_0/boost/coroutine/coroutine.hpp:10:0,  
                 from /usr/boost_1_72_0/boost/coroutine/all.hpp:11,  
                 from /usr/boost_1_72_0/boost/asio/spawn.hpp:19,  
                 from src/WebsocketManager.h:4,  
                 from src/WebsocketManager.cpp:1:  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp: In function ‘typename boost::coroutines::pull_coroutine<R>::iterator boost::coroutines::begin(boost::coroutines::pull_coroutine<Arg>&)’:  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2364:10: error: ‘begin’ is not a member of ‘boost’  
 { return boost::begin( c); }  
          ^  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2364:10: note: suggested alternatives:  
In file included from /usr/include/c++/4.8.2/string:51:0,  
                 from /usr/include/c++/4.8.2/stdexcept:39,  
                 from /usr/include/c++/4.8.2/array:38,  
                 from /usr/include/c++/4.8.2/tuple:39,  
                 from /usr/include/c++/4.8.2/functional:55,  
                 from /usr/include/c++/4.8.2/memory:79,  
                 from /usr/boost_1_72_0/boost/asio/detail/memory.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/handler_invoke_helpers.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/io_object_executor.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/io_object_impl.hpp:20,  
                 from /usr/boost_1_72_0/boost/asio/basic_socket.hpp:21,  
                 from /usr/boost_1_72_0/boost/asio/basic_socket_acceptor.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/ip/tcp.hpp:19,  
                 from src/WebsocketManager.h:3,  
                 from src/WebsocketManager.cpp:1:  
/usr/include/c++/4.8.2/bits/range_access.h:87:5: note:   ‘std::begin’  
     begin(_Tp (&__arr)[_Nm])  
     ^  
In file included from /usr/boost_1_72_0/boost/coroutine/coroutine.hpp:10:0,  
                 from /usr/boost_1_72_0/boost/coroutine/all.hpp:11,  
                 from /usr/boost_1_72_0/boost/asio/spawn.hpp:19,  
                 from src/WebsocketManager.h:4,  
                 from src/WebsocketManager.cpp:1:  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2363:1: note:   ‘boost::coroutines::begin’  
 begin( pull_coroutine< R > & c)  
 ^  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp: In function ‘typename boost::coroutines::pull_coroutine<R>::const_iterator boost::coroutines::begin(const boost::coroutines::pull_coroutine<Arg>&)’:  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2369:10: error: ‘begin’ is not a member of ‘boost’  
 { return boost::begin( c); }  
          ^  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2369:10: note: suggested alternatives:  
In file included from /usr/include/c++/4.8.2/string:51:0,  
                 from /usr/include/c++/4.8.2/stdexcept:39,  
                 from /usr/include/c++/4.8.2/array:38,  
                 from /usr/include/c++/4.8.2/tuple:39,  
                 from /usr/include/c++/4.8.2/functional:55,  
                 from /usr/include/c++/4.8.2/memory:79,  
                 from /usr/boost_1_72_0/boost/asio/detail/memory.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/handler_invoke_helpers.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/io_object_executor.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/io_object_impl.hpp:20,  
                 from /usr/boost_1_72_0/boost/asio/basic_socket.hpp:21,  
                 from /usr/boost_1_72_0/boost/asio/basic_socket_acceptor.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/ip/tcp.hpp:19,  
                 from src/WebsocketManager.h:3,  
                 from src/WebsocketManager.cpp:1:  
/usr/include/c++/4.8.2/bits/range_access.h:87:5: note:   ‘std::begin’  
     begin(_Tp (&__arr)[_Nm])  
     ^  
In file included from /usr/boost_1_72_0/boost/coroutine/coroutine.hpp:10:0,  
                 from /usr/boost_1_72_0/boost/coroutine/all.hpp:11,  
                 from /usr/boost_1_72_0/boost/asio/spawn.hpp:19,  
                 from src/WebsocketManager.h:4,  
                 from src/WebsocketManager.cpp:1:  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2368:1: note:   ‘boost::coroutines::begin’  
 begin( pull_coroutine< R > const& c)  
 ^  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp: In function ‘typename boost::coroutines::pull_coroutine<R>::iterator boost::coroutines::end(boost::coroutines::pull_coroutine<Arg>&)’:  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2374:10: error: ‘end’ is not a member of ‘boost’  
 { return boost::end( c); }  
          ^  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2374:10: note: suggested alternatives:  
In file included from /usr/include/c++/4.8.2/string:51:0,  
                 from /usr/include/c++/4.8.2/stdexcept:39,  
                 from /usr/include/c++/4.8.2/array:38,  
                 from /usr/include/c++/4.8.2/tuple:39,  
                 from /usr/include/c++/4.8.2/functional:55,  
                 from /usr/include/c++/4.8.2/memory:79,  
                 from /usr/boost_1_72_0/boost/asio/detail/memory.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/handler_invoke_helpers.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/io_object_executor.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/detail/io_object_impl.hpp:20,  
                 from /usr/boost_1_72_0/boost/asio/basic_socket.hpp:21,  
                 from /usr/boost_1_72_0/boost/asio/basic_socket_acceptor.hpp:19,  
                 from /usr/boost_1_72_0/boost/asio/ip/tcp.hpp:19,  
                 from src/WebsocketManager.h:3,  
                 from src/WebsocketManager.cpp:1:  
/usr/include/c++/4.8.2/bits/range_access.h:97:5: note:   ‘std::end’  
     end(_Tp (&__arr)[_Nm])  
     ^  
In file included from /usr/boost_1_72_0/boost/coroutine/coroutine.hpp:10:0,  
                 from /usr/boost_1_72_0/boost/coroutine/all.hpp:11,  
                 from /usr/boost_1_72_0/boost/asio/spawn.hpp:19,  
                 from src/WebsocketManager.h:4,  
                 from src/WebsocketManager.cpp:1:  
/usr/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2373:1: note:   ‘boost::coroutines::end’  
 end( pull_coroutine< R > & c)  
 ^  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-31 08:36:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1796#issuecomment-569888956  

It is fixed here: https://github.com/boostorg/coroutine/pull/45
