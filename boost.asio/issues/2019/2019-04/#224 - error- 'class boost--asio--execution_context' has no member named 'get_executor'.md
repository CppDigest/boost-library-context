# #224 - error: 'class boost::asio::execution_context' has no member named 'get_executor' [Closed]

> Username: ghost  
> Created at: 2019-04-13 08:51:58 UTC  
> Updated at: 2019-04-14 07:10:31 UTC  
> Closed at: 2019-04-14 07:10:31 UTC  
> Url: https://github.com/boostorg/asio/issues/224  

there are two sockets:  
```  
ip::tcp::socket local;  
ip::tcp::socket remote;  
```  
  
I want to construct remote as  
```  
remote(local.get_executor().context())  
```  
before boost 1.70.0 it works fine  
on boost 1.70.0   
compiler reports:  
```  
In file included from E:/mingw64/include/boost/asio/basic_socket.hpp:21,  
                 from E:/mingw64/include/boost/asio/basic_datagram_socket.hpp:20,  
                 from E:/mingw64/include/boost/asio/ip/udp.hpp:19,  
                 from E:\msocks\src\session\server_session.cpp:3:  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp: In instantiation of 'boost::asio::detail::io_object_impl<IoObjectService, Executor>::io_object_impl(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; IoObjectService = boost::asio::detail::resolver_service<boost::asio::ip::tcp>; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]':  
E:/mingw64/include/boost/asio/ip/basic_resolver.hpp:118:20:   required from 'boost::asio::ip::basic_resolver<InternetProtocol, Executor>::basic_resolver(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; InternetProtocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:\msocks\src\session\server_session.cpp:30:60:   required from here  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp:87:40: error: 'class boost::asio::execution_context' has no member named 'get_executor'  
       implementation_executor_(context.get_executor(),  
                                ~~~~~~~~^~~~~~~~~~~~  
In file included from E:/mingw64/include/boost/asio/basic_socket.hpp:21,  
                 from E:/mingw64/include/boost/asio/basic_socket_acceptor.hpp:19,  
                 from E:/mingw64/include/boost/asio/ip/tcp.hpp:19,  
                 from E:/msocks/include/msocks/endpoint/client.hpp:3,  
                 from E:\msocks\src\endpoint\client.cpp:2:  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp: In instantiation of 'boost::asio::detail::io_object_impl<IoObjectService, Executor>::io_object_impl(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; IoObjectService = boost::asio::detail::win_iocp_socket_service<boost::asio::ip::tcp>; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]':  
E:/mingw64/include/boost/asio/basic_socket.hpp:132:20:   required from 'boost::asio::basic_socket<Protocol, Executor>::basic_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/mingw64/include/boost/asio/basic_stream_socket.hpp:112:47:   required from 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/msocks/include/msocks/session/session.hpp:26:11:   required from here  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp:87:40: error: 'class boost::asio::execution_context' has no member named 'get_executor'  
       implementation_executor_(context.get_executor(),  
                                ~~~~~~~~^~~~~~~~~~~~  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp: In instantiation of 'boost::asio::detail::io_object_impl<IoObjectService, Executor>::io_object_impl(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; IoObjectService = boost::asio::detail::win_iocp_socket_service<boost::asio::ip::tcp>; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]':  
E:/mingw64/include/boost/asio/basic_socket.hpp:132:20:   required from 'boost::asio::basic_socket<Protocol, Executor>::basic_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/mingw64/include/boost/asio/basic_stream_socket.hpp:112:47:   required from 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/msocks/include/msocks/session/session.hpp:26:11:   required from here  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp:87:40: error: 'class boost::asio::execution_context' has no member named 'get_executor'  
In file included from E:/mingw64/include/boost/asio/basic_socket.hpp:21,  
                 from E:/mingw64/include/boost/asio/basic_socket_acceptor.hpp:19,  
                 from E:/mingw64/include/boost/asio/ip/tcp.hpp:19,  
                 from E:/msocks/include/msocks/session/client_session.hpp:4,  
                 from E:\msocks\src\session\client_session.cpp:5:  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp: In instantiation of 'boost::asio::detail::io_object_impl<IoObjectService, Executor>::io_object_impl(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; IoObjectService = boost::asio::detail::win_iocp_socket_service<boost::asio::ip::tcp>; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]':  
E:/mingw64/include/boost/asio/basic_socket.hpp:132:20:   required from 'boost::asio::basic_socket<Protocol, Executor>::basic_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/mingw64/include/boost/asio/basic_stream_socket.hpp:112:47:   required from 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/msocks/include/msocks/session/session.hpp:26:11:   required from here  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp:87:40: error: 'class boost::asio::execution_context' has no member named 'get_executor'  
       implementation_executor_(context.get_executor(),  
                                ~~~~~~~~^~~~~~~~~~~~  
In file included from E:/mingw64/include/boost/asio/basic_socket.hpp:21,  
                 from E:/mingw64/include/boost/asio/basic_socket_acceptor.hpp:19,  
                 from E:/mingw64/include/boost/asio/ip/tcp.hpp:19,  
                 from E:/msocks/include/msocks/endpoint/server.hpp:3,  
                 from E:\msocks\src\endpoint\server.cpp:2:  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp: In instantiation of 'boost::asio::detail::io_object_impl<IoObjectService, Executor>::io_object_impl(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; IoObjectService = boost::asio::detail::win_iocp_socket_service<boost::asio::ip::tcp>; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]':  
E:/mingw64/include/boost/asio/basic_socket.hpp:132:20:   required from 'boost::asio::basic_socket<Protocol, Executor>::basic_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/mingw64/include/boost/asio/basic_stream_socket.hpp:112:47:   required from 'boost::asio::basic_stream_socket<Protocol, Executor>::basic_stream_socket(ExecutionContext&, typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type*) [with ExecutionContext = boost::asio::execution_context; Protocol = boost::asio::ip::tcp; Executor = boost::asio::executor; typename std::enable_if<std::is_convertible<ExecutionContext&, boost::asio::execution_context&>::value>::type = void]'  
E:/msocks/include/msocks/session/session.hpp:26:11:   required from here  
E:/mingw64/include/boost/asio/detail/io_object_impl.hpp:87:40: error: 'class boost::asio::execution_context' has no member named 'get_executor'  
       implementation_executor_(context.get_executor(),  
                                ~~~~~~~~^~~~~~~~~~~~  
mingw32-make.exe[2]: *** [CMakeFiles\msocks.dir\build.make:91: CMakeFiles/msocks.dir/src/endpoint/client.cpp.obj] Error 1  
mingw32-make.exe[2]: *** Waiting for unfinished jobs....  
mingw32-make.exe[2]: *** [CMakeFiles\msocks.dir\build.make:105: CMakeFiles/msocks.dir/src/endpoint/server.cpp.obj] Error 1  
mingw32-make.exe[2]: *** [CMakeFiles\msocks.dir\build.make:63: CMakeFiles/msocks.dir/src/session/client_session.cpp.obj] Error 1  
mingw32-make.exe[2]: *** [CMakeFiles\msocks.dir\build.make:77: CMakeFiles/msocks.dir/src/session/server_session.cpp.obj] Error 1  
mingw32-make.exe[1]: *** [CMakeFiles\Makefile2:72: CMakeFiles/msocks.dir/all] Error 2  
mingw32-make.exe: *** [Makefile:83: all] Error 2  
```

---

## Comment 1

> Username: djarek  
> Created at: 2019-04-13 16:46:18 UTC  
> Url: https://github.com/boostorg/asio/issues/224#issuecomment-482836758  

Sadly this is a breaking change, either construct with this expression:  
```ip::tcp::socket remote(local.get_executor())```  
or change the socket's executor type to `asio::io_context::executor_type`, e.g.:  
```c++  
namespace net = boost::asio;  
net::basic_stream_socket<net::ip::tcp, net::io_context::executor_type> remote(local.get_executor().context());  
```
