# #173 - clang-cl warnings concerning move semantics [Closed]

> Username: computerquip-streamlabs  
> Created at: 2018-11-29 00:19:37 UTC  
> Updated at: 2018-11-29 00:20:58 UTC  
> Closed at: 2018-11-29 00:20:58 UTC  
> Url: https://github.com/boostorg/asio/issues/173  

```  
1>In file included from D:\Projects\slobs-updater\src\update-client.cc:8:  
1>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/asio.hpp:80:  
1>In file included from D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/asio/ip/basic_resolver.hpp:29:  
1>D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/asio/ip/basic_resolver_results.hpp(256): warning : local variable 'tmp' will be copied despite being returned by name [-Wreturn-std-move]  
1>D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/asio/impl/connect.hpp(341):  note: in instantiation of member function 'boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>::begin' requested here  
1>D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/asio/impl/connect.hpp(714):  note: in instantiation of member function 'boost::asio::detail::range_connect_op<boost::asio::ip::tcp, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, boost::asio::detail::default_connect_condition, (lambda at D:\Projects\slobs-updater\src\update-client.cc:506:2)>::operator()' requested here  
1>D:\Projects\slobs-updater\src\update-client.cc(510):  note: in instantiation of function template specialization 'boost::asio::async_connect<boost::asio::ip::tcp, boost::asio::ip::basic_resolver_results<boost::asio::ip::tcp>, (lambda at D:\Projects\slobs-updater\src\update-client.cc:506:2) &>' requested here  
1>D:\Libraries\boost_1_67_0\msvc141-1_67_0-all\include\boost-1_67\boost/asio/ip/basic_resolver_results.hpp(256):  note: call 'std::move' explicitly to avoid copying  
```  
  
Caused with clang-cl from LLVM 7.0 suite.

---

## Comment 1

> Username: computerquip-streamlabs  
> Created at: 2018-11-29 00:20:58 UTC  
> Url: https://github.com/boostorg/asio/issues/173#issuecomment-442657265  

Never mind, I see that was fixed here: d9a0dec854b05abe97887d0ca1e15def92d152fe
