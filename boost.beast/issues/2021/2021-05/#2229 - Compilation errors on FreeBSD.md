# #2229 - Compilation errors on FreeBSD [Closed]

> Username: Tectu  
> Created at: 2021-05-12 13:29:28 UTC  
> Updated at: 2021-09-01 15:23:47 UTC  
> Closed at: 2021-09-01 15:23:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2229  

I have a boost.beast based library which compiles fine on Windows using MSYS2 & GCC 10.3.  
Now I wanted to compile it on FreeBSD but I am getting a series of errors I'm unsure what to make off (see below). Any ideas or anything else that would help me here?  
The code in the library is largely based on the various boost.beast examples, mainly the `example/advanced/server-flex` example.  
  
This is on FreeBSD 13.0-RELEASE 64-bit using GCC 10.3.0.  
I tried this with boost 1.75.0 and 1.76.0 - both result in the same compilation error(s).  
  
Note that I had to manually specify the path to boost when invoking cmake as the official FreeBSD package for the boost-libs is still 1.72.0:  
```shell  
cmake -DBOOST_ROOT=/home/jbo/Downloads/boost_1_75_0  
```  
  
If you'd like to give this a try yourself, the library is available at: https://github.com/tectu/malloy  
  
The compilation errors:  
```none  
Consolidate compiler generated dependencies of target malloy-objs  
[ 28%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/http/connection/connection_detector.cpp.o  
In file included from /usr/local/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:783:1: error: no declaration matches 'auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  783 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:803:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  803 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:825:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
  825 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:846:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
  846 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:867:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
  867 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:888:1: error: no declaration matches 'auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  888 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/basic_stream.hpp:1325:5: note: candidate is: 'template<class Protocol, class Executor, class RatePolicy> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
 1325 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/basic_stream.hpp:908:1: error: no declaration matches 'auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  908 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:16,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/basic_stream.hpp:1448:5: note: candidate is: 'template<class Protocol, class Executor, class RatePolicy> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
 1448 |     async_write_some(  
      |     ^~~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/buffered_read_stream.hpp:357,  
                 from /usr/local/include/boost/beast/core.hpp:19,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/buffered_read_stream.hpp:146:1: error: no declaration matches 'auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  146 | buffered_read_stream<Stream, DynamicBuffer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:19,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/buffered_read_stream.hpp:348:5: note: candidate is: 'template<class Stream, class DynamicBuffer> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  348 |     async_write_some(  
      |     ^~~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/buffered_read_stream.hpp:92:7: note: 'class boost::beast::buffered_read_stream<Stream, DynamicBuffer>' defined here  
   92 | class buffered_read_stream  
      |       ^~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/buffered_read_stream.hpp:357,  
                 from /usr/local/include/boost/beast/core.hpp:19,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/buffered_read_stream.hpp:217:1: error: no declaration matches 'auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  217 | buffered_read_stream<Stream, DynamicBuffer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:19,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/buffered_read_stream.hpp:269:5: note: candidate is: 'template<class Stream, class DynamicBuffer> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  269 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/buffered_read_stream.hpp:92:7: note: 'class boost::beast::buffered_read_stream<Stream, DynamicBuffer>' defined here  
   92 | class buffered_read_stream  
      |       ^~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/flat_stream.hpp:349,  
                 from /usr/local/include/boost/beast/core.hpp:35,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/flat_stream.hpp:149:1: error: no declaration matches 'auto boost::beast::flat_stream<NextLayer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  149 | flat_stream<NextLayer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:35,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/flat_stream.hpp:259:5: note: candidate is: 'template<class NextLayer> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::flat_stream<NextLayer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  259 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/flat_stream.hpp:88:7: note: 'class boost::beast::flat_stream<NextLayer>' defined here  
   88 | class flat_stream  
      |       ^~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core/flat_stream.hpp:349,  
                 from /usr/local/include/boost/beast/core.hpp:35,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/impl/flat_stream.hpp:230:1: error: no declaration matches 'auto boost::beast::flat_stream<NextLayer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  230 | flat_stream<NextLayer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/core.hpp:35,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/flat_stream.hpp:340:5: note: candidate is: 'template<class NextLayer> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::flat_stream<NextLayer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  340 |     async_write_some(  
      |     ^~~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/flat_stream.hpp:88:7: note: 'class boost::beast::flat_stream<NextLayer>' defined here  
   88 | class flat_stream  
      |       ^~~~~~~~~~~  
In file included from /home/jbo/projects/malloy/lib/malloy/http/connection/../routing/router.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/home/jbo/projects/malloy/lib/malloy/http/connection/../routing/../response.hpp:79:16: error: declaration of 'malloy::http::status malloy::http::response::status() const' changes meaning of 'status' [-fpermissive]  
   79 |         status status() const { return result(); }  
      |                ^~~~~~  
In file included from /home/jbo/projects/malloy/lib/malloy/http/connection/../routing/../response.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../routing/router.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/home/jbo/projects/malloy/lib/malloy/http/connection/../routing/../types.hpp:22:11: note: 'status' declared here as 'using status = enum class boost::beast::http::status'  
   22 |     using status = boost::beast::http::status;  
      |           ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2768,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/accept.hpp:547:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(AcceptHandler&&)'  
  547 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:1426:5: note: candidates are: 'template<class NextLayer, bool deflateSupported> template<class Body, class Allocator, class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&, AcceptHandler&&)'  
 1426 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:1361:5: note:                 'template<class NextLayer, bool deflateSupported> template<class ConstBufferSequence, class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(const ConstBufferSequence&, AcceptHandler&&, typename std::enable_if<(! typename boost::beast::http::detail::is_header_impl<ConstBufferSequence>::type::value)>::type*)'  
 1361 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:1293:5: note:                 'template<class NextLayer, bool deflateSupported> template<class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(AcceptHandler&&)'  
 1293 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2768,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/accept.hpp:595:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(const ConstBufferSequence&, AcceptHandler&&, typename std::enable_if<(! typename boost::beast::http::detail::is_header_impl<ConstBufferSequence>::type::value)>::type*)'  
  595 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:1426:5: note: candidates are: 'template<class NextLayer, bool deflateSupported> template<class Body, class Allocator, class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&, AcceptHandler&&)'  
 1426 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:1361:5: note:                 'template<class NextLayer, bool deflateSupported> template<class ConstBufferSequence, class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(const ConstBufferSequence&, AcceptHandler&&, typename std::enable_if<(! typename boost::beast::http::detail::is_header_impl<ConstBufferSequence>::type::value)>::type*)'  
 1361 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:1293:5: note:                 'template<class NextLayer, bool deflateSupported> template<class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(AcceptHandler&&)'  
 1293 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2768,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/accept.hpp:659:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&, AcceptHandler&&)'  
  659 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:1426:5: note: candidates are: 'template<class NextLayer, bool deflateSupported> template<class Body, class Allocator, class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&, AcceptHandler&&)'  
 1426 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:1361:5: note:                 'template<class NextLayer, bool deflateSupported> template<class ConstBufferSequence, class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(const ConstBufferSequence&, AcceptHandler&&, typename std::enable_if<(! typename boost::beast::http::detail::is_header_impl<ConstBufferSequence>::type::value)>::type*)'  
 1361 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:1293:5: note:                 'template<class NextLayer, bool deflateSupported> template<class AcceptHandler>  requires  completion_token_for<AcceptHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(AcceptHandler&&)'  
 1293 |     async_accept(  
      |     ^~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2769,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/close.hpp:387:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_close(const boost::beast::websocket::close_reason&, CloseHandler&&)'  
  387 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:1561:5: note: candidate is: 'template<class NextLayer, bool deflateSupported> template<class CloseHandler>  requires  completion_token_for<CloseHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_close(const boost::beast::websocket::close_reason&, CloseHandler&&)'  
 1561 |     async_close(  
      |     ^~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2770,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/handshake.hpp:273:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_handshake(boost::beast::string_view, boost::beast::string_view, HandshakeHandler&&)'  
  273 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:969:5: note: candidates are: 'template<class NextLayer, bool deflateSupported> template<class HandshakeHandler>  requires  completion_token_for<HandshakeHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_handshake(boost::beast::websocket::response_type&, boost::beast::string_view, boost::beast::string_view, HandshakeHandler&&)'  
  969 |     async_handshake(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:883:5: note:                 'template<class NextLayer, bool deflateSupported> template<class HandshakeHandler>  requires  completion_token_for<HandshakeHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_handshake(boost::beast::string_view, boost::beast::string_view, HandshakeHandler&&)'  
  883 |     async_handshake(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2770,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/handshake.hpp:298:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_handshake(boost::beast::websocket::response_type&, boost::beast::string_view, boost::beast::string_view, HandshakeHandler&&)'  
  298 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:969:5: note: candidates are: 'template<class NextLayer, bool deflateSupported> template<class HandshakeHandler>  requires  completion_token_for<HandshakeHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_handshake(boost::beast::websocket::response_type&, boost::beast::string_view, boost::beast::string_view, HandshakeHandler&&)'  
  969 |     async_handshake(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:883:5: note:                 'template<class NextLayer, bool deflateSupported> template<class HandshakeHandler>  requires  completion_token_for<HandshakeHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_handshake(boost::beast::string_view, boost::beast::string_view, HandshakeHandler&&)'  
  883 |     async_handshake(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2771,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/ping.hpp:293:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_ping(const ping_data&, WriteHandler&&)'  
  293 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:1663:5: note: candidate is: 'template<class NextLayer, bool deflateSupported> template<class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_ping(const ping_data&, WriteHandler&&)'  
 1663 |     async_ping(  
      |     ^~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2771,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/ping.hpp:311:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_pong(const ping_data&, WriteHandler&&)'  
  311 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:1771:5: note: candidate is: 'template<class NextLayer, bool deflateSupported> template<class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_pong(const ping_data&, WriteHandler&&)'  
 1771 |     async_pong(  
      |     ^~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2772,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/read.hpp:807:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read(DynamicBuffer&, ReadHandler&&)'  
  807 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:1933:5: note: candidate is: 'template<class NextLayer, bool deflateSupported> template<class DynamicBuffer, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read(DynamicBuffer&, ReadHandler&&)'  
 1933 |     async_read(  
      |     ^~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2772,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/read.hpp:881:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read_some(DynamicBuffer&, std::size_t, ReadHandler&&)'  
  881 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:2287:5: note: candidates are: 'template<class NextLayer, bool deflateSupported> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
 2287 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:2112:5: note:                 'template<class NextLayer, bool deflateSupported> template<class DynamicBuffer, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read_some(DynamicBuffer&, std::size_t, ReadHandler&&)'  
 2112 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2772,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/read.hpp:1264:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
 1264 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:2287:5: note: candidates are: 'template<class NextLayer, bool deflateSupported> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
 2287 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:2112:5: note:                 'template<class NextLayer, bool deflateSupported> template<class DynamicBuffer, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read_some(DynamicBuffer&, std::size_t, ReadHandler&&)'  
 2112 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2774,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/write.hpp:705:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_write_some(bool, const ConstBufferSequence&, WriteHandler&&)'  
  705 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:2533:5: note: candidate is: 'template<class NextLayer, bool deflateSupported> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_write_some(bool, const ConstBufferSequence&, WriteHandler&&)'  
 2533 |     async_write_some(  
      |     ^~~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /usr/local/include/boost/beast/websocket/stream.hpp:2774,  
                 from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/impl/write.hpp:761:1: error: no declaration matches 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_write(const ConstBufferSequence&, WriteHandler&&)'  
  761 | stream<NextLayer, deflateSupported>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/beast/websocket.hpp:18,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:6,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/usr/local/include/boost/beast/websocket/stream.hpp:2410:5: note: candidate is: 'template<class NextLayer, bool deflateSupported> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_write(const ConstBufferSequence&, WriteHandler&&)'  
 2410 |     async_write(  
      |     ^~~~~~~~~~~  
/usr/local/include/boost/beast/websocket/stream.hpp:127:7: note: 'class boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >' defined here  
  127 | class stream  
      |       ^~~~~~  
In file included from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp: In instantiation of 'void malloy::http::server::connection<Derived>::do_read() [with Derived = malloy::http::server::connection_plain]':  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:67:34:   required from here  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:142:43: error: use of 'auto boost::beast::http::async_read(AsyncReadStream&, DynamicBuffer&, boost::beast::http::basic_parser<isRequest>&, ReadHandler&&) [with AsyncReadStream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char> >; bool isRequest = true; ReadHandler = boost::beast::detail::bind_front_wrapper<void (malloy::http::server::connection<malloy::http::server::connection_plain>::*)(boost::system::error_code, long unsigned int), std::shared_ptr<malloy::http::server::connection_plain> >]' before deduction of 'auto'  
  142 |             boost::beast::http::async_read(  
      |             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  143 |                 derived().m_stream,  
      |                 ~~~~~~~~~~~~~~~~~~~          
  144 |                 m_buffer,  
      |                 ~~~~~~~~~                    
  145 |                 *m_parser,  
      |                 ~~~~~~~~~~                   
  146 |                 boost::beast::bind_front_handler(  
      |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                     &connection::on_read,  
      |                     ~~~~~~~~~~~~~~~~~~~~~    
  148 |                     derived().shared_from_this()  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  149 |                 )  
      |                 ~                            
  150 |             );  
      |             ~                                
In file included from /usr/local/include/boost/beast/core.hpp:26,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:1:  
/usr/local/include/boost/beast/core/detect_ssl.hpp: In instantiation of 'typename boost::asio::async_result<typename std::decay<TeardownHandler>::type, void(boost::system::error_code, bool)>::return_type boost::beast::async_detect_ssl(AsyncReadStream&, DynamicBuffer&, CompletionToken&&) [with AsyncReadStream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char> >; CompletionToken = boost::beast::detail::bind_front_wrapper<void (malloy::http::server::connection_detector::*)(boost::system::error_code, bool), std::shared_ptr<malloy::http::server::connection_detector> >; typename boost::asio::async_result<typename std::decay<TeardownHandler>::type, void(boost::system::error_code, bool)>::return_type = void]':  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:43:5:   required from here  
/usr/local/include/boost/beast/core/detect_ssl.hpp:414:48: error: static assertion failed: SyncReadStream type requirements not met  
  414 |         is_async_read_stream<AsyncReadStream>::value,  
      |                                                ^~~~~  
In file included from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp: In instantiation of 'void malloy::http::server::connection<Derived>::send_lambda::operator()(boost::beast::http::message<isRequest, Body, Fields>&&) const [with bool isRequest = false; Body = boost::beast::http::basic_string_body<char>; Fields = boost::beast::http::basic_fields<std::allocator<char> >; Derived = malloy::http::server::connection_plain]':  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:216:23:   required from 'void malloy::http::server::connection<Derived>::on_read(boost::beast::error_code, std::size_t) [with Derived = malloy::http::server::connection_plain; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:147:21:   required from 'void malloy::http::server::connection<Derived>::do_read() [with Derived = malloy::http::server::connection_plain]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:67:34:   required from here  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:68:48: error: use of 'auto boost::beast::http::async_write(AsyncWriteStream&, const boost::beast::http::message<isRequest, Body, Fields>&, WriteHandler&&, typename std::enable_if<(! boost::beast::http::is_mutable_body_writer<Body>::value)>::type*) [with AsyncWriteStream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; bool isRequest = false; Body = boost::beast::http::basic_string_body<char>; Fields = boost::beast::http::basic_fields<std::allocator<char> >; WriteHandler = boost::beast::detail::bind_front_wrapper<void (malloy::http::server::connection<malloy::http::server::connection_plain>::*)(bool, boost::system::error_code, long unsigned int), std::shared_ptr<malloy::http::server::connection_plain>, bool>; typename std::enable_if<(! boost::beast::http::is_mutable_body_writer<Body>::value)>::type = void]' before deduction of 'auto'  
   68 |                 boost::beast::http::async_write(  
      |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
   69 |                     m_self.derived().m_stream,  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~~~~    
   70 |                     *sp,  
      |                     ~~~~                          
   71 |                     boost::beast::bind_front_handler(  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   72 |                         &connection::on_write,  
      |                         ~~~~~~~~~~~~~~~~~~~~~~    
   73 |                         m_self.derived().shared_from_this(),  
      |                         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   74 |                         sp->need_eof()  
      |                         ~~~~~~~~~~~~~~            
   75 |                     )  
      |                     ~                             
   76 |                 );  
      |                 ~                                 
In file included from /home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:5,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:3,  
                 from /home/jbo/projects/malloy/lib/malloy/http/connection/connection_detector.cpp:2:  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp: In instantiation of 'void malloy::websocket::server::connection<Derived>::do_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; Derived = malloy::websocket::server::connection_plain; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]':  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:48:22:   required from 'void malloy::websocket::server::connection<Derived>::run(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; Derived = malloy::websocket::server::connection_plain; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:58:36:   required from 'void malloy::websocket::server::make_websocket_connection(std::shared_ptr<spdlog::logger>, malloy::websocket::handler_type, boost::beast::tcp_stream, boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; malloy::websocket::handler_type = std::function<void(std::__cxx11::basic_string<char>, std::function<void(std::__cxx11::basic_string<char>&&)>)>; boost::beast::tcp_stream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:199:61:   required from 'void malloy::http::server::connection<Derived>::on_read(boost::beast::error_code, std::size_t) [with Derived = malloy::http::server::connection_plain; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:147:21:   required from 'void malloy::http::server::connection<Derived>::do_read() [with Derived = malloy::http::server::connection_plain]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:67:34:   required from here  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:111:44: error: use of 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&, AcceptHandler&&) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; AcceptHandler = boost::beast::detail::bind_front_wrapper<void (malloy::websocket::server::connection<malloy::websocket::server::connection_plain>::*)(boost::system::error_code), std::shared_ptr<malloy::websocket::server::connection_plain> >; NextLayer = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; bool deflateSupported = true; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]' before deduction of 'auto'  
  111 |             derived().stream().async_accept(  
      |             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  112 |                 req,  
      |                 ~~~~                          
  113 |                 boost::beast::bind_front_handler(  
      |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  114 |                     &connection::on_accept,  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~   
  115 |                     derived().shared_from_this()  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  116 |                 )  
      |                 ~                             
  117 |             );  
      |             ~                                 
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp: In instantiation of 'void malloy::websocket::server::connection<Derived>::do_read() [with Derived = malloy::websocket::server::connection_plain]':  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:132:13:   required from 'void malloy::websocket::server::connection<Derived>::on_accept(boost::beast::error_code) [with Derived = malloy::websocket::server::connection_plain; boost::beast::error_code = boost::system::error_code]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:114:21:   required from 'void malloy::websocket::server::connection<Derived>::do_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; Derived = malloy::websocket::server::connection_plain; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:48:22:   required from 'void malloy::websocket::server::connection<Derived>::run(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; Derived = malloy::websocket::server::connection_plain; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:58:36:   required from 'void malloy::websocket::server::make_websocket_connection(std::shared_ptr<spdlog::logger>, malloy::websocket::handler_type, boost::beast::tcp_stream, boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; malloy::websocket::handler_type = std::function<void(std::__cxx11::basic_string<char>, std::function<void(std::__cxx11::basic_string<char>&&)>)>; boost::beast::tcp_stream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:199:61:   required from 'void malloy::http::server::connection<Derived>::on_read(boost::beast::error_code, std::size_t) [with Derived = malloy::http::server::connection_plain; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:147:21:   required from 'void malloy::http::server::connection<Derived>::do_read() [with Derived = malloy::http::server::connection_plain]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:67:34:   required from here  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:141:42: error: use of 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_read(DynamicBuffer&, ReadHandler&&) [with DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char> >; ReadHandler = boost::beast::detail::bind_front_wrapper<void (malloy::websocket::server::connection<malloy::websocket::server::connection_plain>::*)(boost::system::error_code, long unsigned int), std::shared_ptr<malloy::websocket::server::connection_plain> >; NextLayer = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; bool deflateSupported = true]' before deduction of 'auto'  
  141 |             derived().stream().async_read(  
      |             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  142 |                 m_buffer,  
      |                 ~~~~~~~~~                   
  143 |                 boost::beast::bind_front_handler(  
      |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  144 |                     &connection::on_read,  
      |                     ~~~~~~~~~~~~~~~~~~~~~   
  145 |                     derived().shared_from_this()  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  146 |                 )  
      |                 ~                           
  147 |             );  
      |             ~                               
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp: In instantiation of 'void malloy::websocket::server::connection<Derived>::write(std::string&&) [with Derived = malloy::websocket::server::connection_plain; std::string = std::__cxx11::basic_string<char>]':  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:182:50:   required from 'void malloy::websocket::server::connection<Derived>::on_read(boost::beast::error_code, std::size_t) [with Derived = malloy::websocket::server::connection_plain; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:144:21:   required from 'void malloy::websocket::server::connection<Derived>::do_read() [with Derived = malloy::websocket::server::connection_plain]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:132:13:   required from 'void malloy::websocket::server::connection<Derived>::on_accept(boost::beast::error_code) [with Derived = malloy::websocket::server::connection_plain; boost::beast::error_code = boost::system::error_code]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:114:21:   required from 'void malloy::websocket::server::connection<Derived>::do_accept(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; Derived = malloy::websocket::server::connection_plain; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:48:22:   required from 'void malloy::websocket::server::connection<Derived>::run(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; Derived = malloy::websocket::server::connection_plain; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection_plain.hpp:58:36:   required from 'void malloy::websocket::server::make_websocket_connection(std::shared_ptr<spdlog::logger>, malloy::websocket::handler_type, boost::beast::tcp_stream, boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; malloy::websocket::handler_type = std::function<void(std::__cxx11::basic_string<char>, std::function<void(std::__cxx11::basic_string<char>&&)>)>; boost::beast::tcp_stream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:199:61:   required from 'void malloy::http::server::connection<Derived>::on_read(boost::beast::error_code, std::size_t) [with Derived = malloy::http::server::connection_plain; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection.hpp:147:21:   required from 'void malloy::http::server::connection<Derived>::do_read() [with Derived = malloy::http::server::connection_plain]'  
/home/jbo/projects/malloy/lib/malloy/http/connection/connection_plain.hpp:67:34:   required from here  
/home/jbo/projects/malloy/lib/malloy/http/connection/../../websocket/connection/connection.hpp:65:43: error: use of 'auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_write(const ConstBufferSequence&, WriteHandler&&) [with ConstBufferSequence = boost::asio::mutable_buffers_1; WriteHandler = boost::beast::detail::bind_front_wrapper<void (malloy::websocket::server::connection<malloy::websocket::server::connection_plain>::*)(boost::system::error_code, long unsigned int), std::shared_ptr<malloy::websocket::server::connection_plain> >; NextLayer = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; bool deflateSupported = true]' before deduction of 'auto'  
   65 |             derived().stream().async_write(  
      |             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
   66 |                 boost::asio::buffer(payload),  
      |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   67 |                 boost::beast::bind_front_handler(  
      |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   68 |                     &connection::on_write,  
      |                     ~~~~~~~~~~~~~~~~~~~~~~   
   69 |                     derived().shared_from_this()  
      |                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   70 |                 )  
      |                 ~                            
   71 |             );  
      |             ~                                
*** Error code 1  
  
Stop.  
make[2]: stopped in /usr/home/jbo/projects/malloy/.build  
*** Error code 1  
  
Stop.  
make[1]: stopped in /usr/home/jbo/projects/malloy/.build  
*** Error code 1  
  
Stop.  
make: stopped in /usr/home/jbo/projects/malloy/.build  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-12 13:48:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839787714  

Hi there, thanks for the comprehensive error message and the git repo. I'll look into this right away.  
  
One question, if you look in the CMakeCache.txt file, can you check that the correct version of boost was actually found?  
  
I'll get cracking in the meantime.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-05-12 13:54:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839792161  

I get different errors with gcc11:  
```  
[ 89%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/listener.cpp.o  
In file included from /home/rhodges/github/Tectu/malloy/lib/malloy/http/generator.cpp:2:  
/home/rhodges/github/Tectu/malloy/lib/malloy/http/response.hpp:79:16: error: declaration of ‘malloy::http::status malloy::http::response::status() const’ changes meaning of ‘status’ [-fpermissive]  
   79 |         status status() const { return result(); }  
      |                ^~~~~~  
In file included from /home/rhodges/github/Tectu/malloy/lib/malloy/http/generator.hpp:6,  
                 from /home/rhodges/github/Tectu/malloy/lib/malloy/http/generator.cpp:1:  
/home/rhodges/github/Tectu/malloy/lib/malloy/http/types.hpp:22:11: note: ‘status’ declared here as ‘using status = enum class boost::beast::http::status’  
   22 |     using status = boost::beast::http::status;  
      |           ^~~~~~  
gmake[3]: *** [lib/malloy/CMakeFiles/malloy-objs.dir/build.make:134: lib/malloy/CMakeFiles/malloy-objs.dir/http/generator.cpp.o] Error 1  
gmake[3]: *** Waiting for unfinished jobs....  
```

---

## Comment 3

> Username: Tectu  
> Created at: 2021-05-12 14:22:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839814197  

> One question, if you look in the CMakeCache.txt file, can you check that the correct version of boost was actually found?  
  
Yeah. I actually wanted to include that information in the original post but didn't want to bloat it too much. CMake reports that it found the correct/suitable boost libraries based on the `-DBOOST_ROOT=` setting.  
  
> I get different errors with gcc11:  
  
I'll get that fixed. As mentioned in another issue this library is in a very early stage and has a lot of rough edges. Getting it to compile on something other than Windows/MSYS2 is part of the undertaking of polishing it.  
  
Unfortunately I cannot easily compile this with anything other than gcc 10.2 until I am able to build it on my FreeBSD machine :p

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-05-12 14:43:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839830213  

I'll spin up a docker. What's the latest freebsd docker image?

---

## Comment 5

> Username: Tectu  
> Created at: 2021-05-12 14:50:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839836182  

I'm sorry, I wouldn't be able to help you there.  
All my FreeBSD machines are classic installs directly on hardware.  
  
Is there something else I can be helpful with?

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-12 15:23:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839862318  

Created a PR so that it at least builds on gcc11, -std=c++20  
  
See if that changes anything. I don't have a spare machine to install freebsd.  
Best I can do is fire up an old docker of fedora and try gcc10

---

## Comment 7

> Username: Tectu  
> Created at: 2021-05-12 15:29:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839867381  

Thank you for the PR - that's appreciated!  
  
Unfortunately, the problem reported in the first post remains.  
If you have something like VirtualBox or VMware available you might grab one of the virtual images from https://www.freebsd.org/where/  
See the "Virtual Machine Images" under the "FreeBSD 13.0-Release" section.  
  
Alternatively I can either setup a pre-configured VM with everything installed ready to issue 'make' and provide you with that VM image or I can setup a virtual host and give you SSH access to the machine.  
Would it be okay for you to proceed either of those options?

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-05-12 15:45:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839879444  

I get this on gcc10 with boost 1.73:  
```  
 28%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/listener.cpp.o  
/work/malloy/lib/malloy/listener.cpp: In member function ‘void malloy::server::listener::run()’:  
/work/malloy/lib/malloy/listener.cpp:74:18: error: ‘dispatch’ is not a member of ‘boost::asio’  
   74 |     boost::asio::dispatch(  
      |                  ^~~~~~~~  
make[2]: *** [lib/malloy/CMakeFiles/malloy-objs.dir/build.make:186: lib/malloy/CMakeFiles/malloy-objs.dir/listener.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:492: lib/malloy/CMakeFiles/malloy-objs.dir/all] Error 2  
make: *** [Makefile:103: all] Error 2  
```  
I'll try with latest

---

## Comment 9

> Username: Tectu  
> Created at: 2021-05-12 15:53:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839885084  

The thing I can tell you for sure is that compiling malloy using the following conditions work:  
  
- Windows 10 64-bit with MSYS2  
- GCC `10.2` / `10.3`  
- CMake `3.17` / `3.20.2`  
- Boost `1.75.0` (I think this used to work with `1.73.0` but can't confirm easily)  
  
Once I have this compiling on FreeBSD I will setup CI/CD to test various configurations which should quickly increase the quality of the library.

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-05-12 15:56:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839887810  

"Works on my machine!"  
  
```  
[root@931c98cc8547 work]# cmake -Hmalloy -Bbuild -DCMAKE_CXX_STANDARD=20  
-- The CXX compiler identification is GNU 10.3.1  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Boost: /usr/local/lib/cmake/Boost-1.76.0/BoostConfig.cmake (found suitable version "1.76.0", minimum required is "1.73.0")    
-- Build spdlog: 1.8.3  
-- Looking for C++ include pthread.h  
-- Looking for C++ include pthread.h - found  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Failed  
-- Looking for pthread_create in pthreads  
-- Looking for pthread_create in pthreads - not found  
-- Looking for pthread_create in pthread  
-- Looking for pthread_create in pthread - found  
-- Found Threads: TRUE    
-- Build type: Release  
  
---------------------  
Malloy configuration:  
  Build:  
    Examples : ON  
    Tests    : ON  
  Features:  
    HTML     : ON  
    TLS      : OFF  
---------------------  
  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /work/build  
[root@931c98cc8547 work]# cd build/  
[root@931c98cc8547 build]# vim  
  
Scanning dependencies of target spdlog  
[  3%] Building CXX object _deps/spdlog-build/CMakeFiles/spdlog.dir/src/spdlog.cpp.o  
[  6%] Building CXX object _deps/spdlog-build/CMakeFiles/spdlog.dir/src/stdout_sinks.cpp.o  
[  9%] Building CXX object _deps/spdlog-build/CMakeFiles/spdlog.dir/src/color_sinks.cpp.o  
[ 12%] Building CXX object _deps/spdlog-build/CMakeFiles/spdlog.dir/src/file_sinks.cpp.o  
[ 15%] Building CXX object _deps/spdlog-build/CMakeFiles/spdlog.dir/src/async.cpp.o  
[ 18%] Building CXX object _deps/spdlog-build/CMakeFiles/spdlog.dir/src/cfg.cpp.o  
[ 21%] Building CXX object _deps/spdlog-build/CMakeFiles/spdlog.dir/src/fmt.cpp.o  
[ 25%] Linking CXX static library libspdlog.a  
[ 25%] Built target spdlog  
Scanning dependencies of target malloy-objs  
[ 28%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/http/connection/connection_detector.cpp.o  
[ 31%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/http/routing/router.cpp.o  
[ 34%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/http/session/manager.cpp.o  
[ 37%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/http/cookie.cpp.o  
[ 40%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/http/generator.cpp.o  
[ 43%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/http/uri.cpp.o  
[ 46%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/html/form.cpp.o  
[ 50%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/controller.cpp.o  
[ 53%] Building CXX object lib/malloy/CMakeFiles/malloy-objs.dir/listener.cpp.o  
[ 53%] Built target malloy-objs  
Scanning dependencies of target malloy-example-basic  
[ 56%] Building CXX object examples/examples/basic/CMakeFiles/malloy-example-basic.dir/main.cpp.o  
[ 59%] Linking CXX executable malloy-example-basic  
[ 59%] Built target malloy-example-basic  
Scanning dependencies of target malloy-example-html-form  
[ 62%] Building CXX object examples/examples/html_form/CMakeFiles/malloy-example-html-form.dir/main.cpp.o  
[ 65%] Linking CXX executable malloy-example-html-form  
[ 65%] Built target malloy-example-html-form  
Scanning dependencies of target malloy-example-nestedrouters  
[ 68%] Building CXX object examples/examples/nested_routers/CMakeFiles/malloy-example-nestedrouters.dir/main.cpp.o  
[ 71%] Linking CXX executable malloy-example-nestedrouters  
[ 71%] Built target malloy-example-nestedrouters  
Scanning dependencies of target malloy-example-session  
[ 75%] Building CXX object examples/examples/session/CMakeFiles/malloy-example-session.dir/main.cpp.o  
[ 78%] Linking CXX executable malloy-example-session  
[ 78%] Built target malloy-example-session  
Scanning dependencies of target malloy-websocket  
[ 81%] Building CXX object examples/examples/websocket/CMakeFiles/malloy-websocket.dir/main.cpp.o  
[ 84%] Linking CXX executable malloy-websocket  
[ 84%] Built target malloy-websocket  
Scanning dependencies of target malloy-tests  
[ 87%] Building CXX object test/CMakeFiles/malloy-tests.dir/test_suites/components/http_generator.cpp.o  
[ 90%] Building CXX object test/CMakeFiles/malloy-tests.dir/test_suites/components/router.cpp.o  
[ 93%] Building CXX object test/CMakeFiles/malloy-tests.dir/test_suites/components/uri.cpp.o  
[ 96%] Building CXX object test/CMakeFiles/malloy-tests.dir/test_main.cpp.o  
[100%] Linking CXX executable malloy-tests  
[100%] Built target malloy-tests  
```  
  
```  
[root@931c98cc8547 build]# g++ --version  
g++ (GCC) 10.3.1 20210422 (Red Hat 10.3.1-1)  
Copyright (C) 2020 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-05-12 15:58:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-839889555  

:point_up: this was on a fedora 33 docker image

---

## Comment 12

> Username: Tectu  
> Created at: 2021-05-14 15:57:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-841333837  

Well, knowing that it compiles on Fedora without problems is already helpful & good to know - thank you!  
  
Is there anything you can point me towards for figuring out why the build on FreeBSD fails?

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-05-14 19:45:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-841464316  

I’m at a loss at the moment I’m afraid. I will need to buy another hard drive and install freebsd to find out.

---

## Comment 14

> Username: Tectu  
> Created at: 2021-05-15 11:33:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-841643852  

How about a VM? :)

---

## Comment 15

> Username: madmongo1  
> Created at: 2021-05-15 11:54:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-841646293  

Disk space still the issue, plus VMware pollutes the machine horribly. Nothing ever seems to work properly after I install it.

---

## Comment 16

> Username: TheStormN  
> Created at: 2021-06-03 19:02:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-854106037  

You should try VirtualBox then. :)  
  
Also you can use more native tools, depending on the OS you are using. For Windows - Hyper-V, for Linux - QEMU, etc.

---

## Comment 17

> Username: Tectu  
> Created at: 2021-09-01 15:23:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2229#issuecomment-910392207  

I've revisited this - I'm able to compile on FreeBSD without any problems. This must have been an issue in my application/library code.  
  
Unfortunately, I don't have a way to look for the code which introduced this problem without checking pretty much ever commit in the history.  
  
@madmongo1 Thank you for your efforts!
