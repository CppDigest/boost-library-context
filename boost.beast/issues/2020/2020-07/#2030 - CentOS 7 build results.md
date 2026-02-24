# #2030 - CentOS 7 build results [Closed]

> Username: sdarwin  
> Created at: 2020-07-21 19:33:34 UTC  
> Updated at: 2020-07-24 12:06:55 UTC  
> Closed at: 2020-07-24 12:06:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2030  

### Version of Beast  
  
299  
### Steps necessary to reproduce the problem  
  
(from beast/pull/2028)  
  
docker build -f .dockers/centos-7/Dockerfile .  
  
### Summary of result  
  
```  
...failed updating 4 targets...  
...skipped 4 targets...  
...updated 9392 targets...  
  
  
clang-linux.compile.c++.without-pch bin.v2/libs/beast/example/echo-op/clang-linux-3.4.2/release/cxxstd-11-iso/threading-multi/visibility-hidden/echo_op.o  
libs/beast/example/echo-op/echo_op.cpp:367:19: error: no matching constructor for initialization of 'endpoint_type' (aka 'basic_endpoint<boost::asio::ip::tcp>')  
    endpoint_type ep{address, port};  
                  ^ ~~~~~~~~~~~~~~~  
./boost/asio/ip/basic_endpoint.hpp:81:3: note: candidate constructor not viable: no known conversion from 'const std::initializer_list<boost::asio::ip::address>' to 'const boost::asio::ip::tcp' for 1st argument  
  basic_endpoint(const InternetProtocol& internet_protocol,  
  ^  
```
