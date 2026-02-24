# #2043 - Compile error when using C++20 with GCC 10.1.0 [Closed]

> Username: gracicot  
> Created at: 2020-07-31 14:12:42 UTC  
> Updated at: 2020-07-31 14:38:43 UTC  
> Closed at: 2020-07-31 14:38:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2043  

### Version of Beast  
  
    #define BOOST_BEAST_VERSION 290  
  
### Steps necessary to reproduce the problem  
  
Save this line in a file:  
  
    #include <boost/beast/core.hpp>  
  
Compile using GCC:  
  
    g++ -std=c++20 test.cpp  
  
### All relevant compiler information  
  
```  
$ g++ --version  
g++ (GCC) 10.1.0  
Copyright (C) 2020 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
It seem to work well with `-std=c++17`. Is there a workaround for this problem?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-31 14:14:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2043#issuecomment-667141425  

Well what is the error?

---

## Comment 2

> Username: gracicot  
> Created at: 2020-07-31 14:20:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2043#issuecomment-667144303  

Oops, embarassing. Here's the error:  
```  
$ g++ -std=c++20 test.cpp -I/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/basic_stream.hpp:781:1: error: no declaration matches 'auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  781 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/basic_stream.hpp:801:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  801 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/basic_stream.hpp:823:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
  823 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/basic_stream.hpp:844:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
  844 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/basic_stream.hpp:865:1: error: no declaration matches 'typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
  865 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1202:5: note: candidates are: 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class ConnectCondition, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, ConnectCondition, IteratorConnectHandler&&)'  
 1202 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1141:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class Iterator, class IteratorConnectHandler>  requires  completion_token_for<IteratorConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, Iterator)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(Iterator, Iterator, IteratorConnectHandler&&)'  
 1141 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1083:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class ConnectCondition, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, ConnectCondition, RangeConnectHandler&&)'  
 1083 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:990:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class EndpointSequence, class RangeConnectHandler, class>  requires  completion_token_for<RangeConnectHandler, void()> typename boost::asio::async_result<typename std::decay<RangeConnectHandler>::type, void(boost::system::error_code, typename Protocol::endpoint)>::return_type boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const EndpointSequence&, RangeConnectHandler&&)'  
  990 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:928:5: note:                 'template<class Protocol, class Executor, class RatePolicy> template<class ConnectHandler>  requires  completion_token_for<ConnectHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  928 |     async_connect(  
      |     ^~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/basic_stream.hpp:886:1: error: no declaration matches 'auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  886 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1325:5: note: candidate is: 'template<class Protocol, class Executor, class RatePolicy> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
 1325 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1457,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/basic_stream.hpp:906:1: error: no declaration matches 'auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  906 | basic_stream<Protocol, Executor, RatePolicy>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:16,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:1448:5: note: candidate is: 'template<class Protocol, class Executor, class RatePolicy> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
 1448 |     async_write_some(  
      |     ^~~~~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/basic_stream.hpp:203:7: note: 'class boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >' defined here  
  203 | class basic_stream  
      |       ^~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/buffered_read_stream.hpp:357,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:19,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/buffered_read_stream.hpp:146:1: error: no declaration matches 'auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  146 | buffered_read_stream<Stream, DynamicBuffer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:19,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/buffered_read_stream.hpp:348:5: note: candidate is: 'template<class Stream, class DynamicBuffer> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  348 |     async_write_some(  
      |     ^~~~~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/buffered_read_stream.hpp:92:7: note: 'class boost::beast::buffered_read_stream<Stream, DynamicBuffer>' defined here  
   92 | class buffered_read_stream  
      |       ^~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/buffered_read_stream.hpp:357,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:19,  
                 from test.cpp:1:  
/home/user.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/buffered_read_stream.hpp:217:1: error: no declaration matches 'auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  217 | buffered_read_stream<Stream, DynamicBuffer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:19,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/buffered_read_stream.hpp:269:5: note: candidate is: 'template<class Stream, class DynamicBuffer> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::buffered_read_stream<Stream, DynamicBuffer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  269 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/buffered_read_stream.hpp:92:7: note: 'class boost::beast::buffered_read_stream<Stream, DynamicBuffer>' defined here  
   92 | class buffered_read_stream  
      |       ^~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/flat_stream.hpp:349,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:35,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/flat_stream.hpp:149:1: error: no declaration matches 'auto boost::beast::flat_stream<NextLayer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  149 | flat_stream<NextLayer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:35,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/flat_stream.hpp:259:5: note: candidate is: 'template<class NextLayer> template<class MutableBufferSequence, class ReadHandler>  requires  completion_token_for<ReadHandler, void()> auto boost::beast::flat_stream<NextLayer>::async_read_some(const MutableBufferSequence&, ReadHandler&&)'  
  259 |     async_read_some(  
      |     ^~~~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/flat_stream.hpp:88:7: note: 'class boost::beast::flat_stream<NextLayer>' defined here  
   88 | class flat_stream  
      |       ^~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/flat_stream.hpp:349,  
                 from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:35,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/impl/flat_stream.hpp:230:1: error: no declaration matches 'auto boost::beast::flat_stream<NextLayer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  230 | flat_stream<NextLayer>::  
      | ^~~~~~~~~~~~~~~~~~~~~~  
In file included from /home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core.hpp:35,  
                 from test.cpp:1:  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/flat_stream.hpp:340:5: note: candidate is: 'template<class NextLayer> template<class ConstBufferSequence, class WriteHandler>  requires  completion_token_for<WriteHandler, void()> auto boost::beast::flat_stream<NextLayer>::async_write_some(const ConstBufferSequence&, WriteHandler&&)'  
  340 |     async_write_some(  
      |     ^~~~~~~~~~~~~~~~  
/home/user/.conan/data/boost/1.73.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include/boost/beast/core/flat_stream.hpp:88:7: note: 'class boost::beast::flat_stream<NextLayer>' defined here  
   88 | class flat_stream  
      |       ^~~~~~~~~~~  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-07-31 14:21:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2043#issuecomment-667144894  

hI @gracicot,  
  
It's difficult to be sure without seeing the error. However, there is a problem with boost1.73 and c++20 on gcc. This is due to a compiler capability detection issue in Asio.  
The background here is that the syntax for concepts changed when concepts-ts was standardised.  
  
If it is that, you can workaround it by compiling with command line option  
`-DBOOST_ASIO_DISABLE_CONCEPTS`

---

## Comment 4

> Username: gracicot  
> Created at: 2020-07-31 14:28:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2043#issuecomment-667148335  

@madmongo1 thank you, that indeed solved the compilation issue. Let me know when I can re-enable concepts.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-07-31 14:33:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2043#issuecomment-667150952  

Hi @gracicot,  
  
Concepts will still be enabled for your compiler, so any other concepts you are using will work (gcc 10 conforms to the c++ standard whereas clang9 used the concepts-ts syntax).  
  
Defining `BOOST_ASIO_DISABLE_CONCEPTS` merely causes Asio not to use concepts to check argument validity, for example when checking completion handler or completion token validity.  
  
Concept checking has been fixed on Asio `master` branch, so as of Boost 1.74 (coming very soon) you will be able to undo this workaround.

---

## Comment 6

> Username: gracicot  
> Created at: 2020-07-31 14:38:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2043#issuecomment-667153611  

@madmongo1 perfect, thank you very much, I will update to Boost 1.74 as soon as it comes out!
