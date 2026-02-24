# #2661 - Build error call to 'async_teardown' is ambiguous with Visual Studio 2022 17.5.2 and clang-cl LLVM 16.0.0 [Closed]

> Username: emmenlau  
> Created at: 2023-03-22 10:47:00 UTC  
> Updated at: 2023-06-20 02:40:22 UTC  
> Closed at: 2023-06-20 02:40:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2661  

I get the following build error since updating Visual Studio 2022 to 17.5.2 and clang-cl LLVM to 16.0.0:  
```  
[build] FAILED: CMakeFiles/NetworkService.dir/src/HTTPWSServer.cc.obj   
[build] C:\ci\LLVM-16.0.0\bin\clang-cl.exe   -TP -DBOOST_ALL_NO_LIB=1 -DBOOST_DATE_TIME_NO_LIB -DBOOST_FILESYSTEM_NO_DEPRECATED=1 -DBOOST_FILESYSTEM_NO_LIB -DBOOST_FILESYSTEM_VERSION=4 -DBOOST_NOWIDE_NO_LIB -DBOOST_PROGRAM_OPTIONS_NO_LIB -DDEBUG -DNOMINMAX -D_DEBUG -D_DEBUG_ -IC:\data\usr-tmp-C10Sk32c193532215c1600\Debug\NetworkService -IC:\Src\NetworkService\include -IC:\Src\NetworkService\src -imsvcC:\data\usr-dst-C10Sk32c193532215c1600\Debug\include -imsvcC:\data\usr-dst-C10Sk32c193532215c1600\Debug\lib /D_ITERATOR_DEBUG_LEVEL=0     /DWINVER=0x0A00 /D_WIN32_WINNT=0x0A00 /DDEBUG     /D_CRT_SECURE_NO_DEPRECATE /D_SILENCE_ALL_CXX17_DEPRECATION_WARNINGS  -DQT_DISABLE_DEPRECATED_BEFORE=0x050F00 -Wextra -Werror=return-type -Werror=uninitialized -Werror=unknown-pragmas -Werror=shadow -Werror=undef -Werror=pointer-arith -Werror=shift-count-overflow -Werror=string-plus-int -Werror=nonportable-include-path -Werror=inconsistent-missing-override -Werror=self-assign -Werror=self-assign-field -Werror=self-move -Werror=braced-scalar-init -Werror=many-braces-around-scalar-init -Werror=deprecated-enum-enum-conversion -Werror=infinite-recursion -Werror=exceptions -Werror=excess-initializers -Werror=return-stack-address -Werror=delete-non-abstract-non-virtual-dtor /W4 -Wno-error=undef -Wno-error=shadow  -march=skylake /std:c++20 /MDd /Z7  -Werror=pessimizing-move -Werror=redundant-move -Werror=range-loop-construct  /DWIN32 /D_WINDOWS /GR /EHsc /Zi /Ob0 /Od /RTC1 -MDd /Zc:__cplusplus -std:c++20 /showIncludes /FoCMakeFiles\NetworkService.dir\src\HTTPWSServer.cc.obj /FdNetworkService.pdb -c -- C:\Src\NetworkService\src\HTTPWSServer.cc  
[build] In file included from C:\Src\NetworkService\src\HTTPWSServer.cc:61:  
[build] In file included from C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/websocket.hpp:18:  
[build] In file included from C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/websocket/stream.hpp:2857:  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/websocket/impl/read.hpp(695,17): error: call to 'async_teardown' is ambiguous  
[build]                 async_teardown(impl.role, impl.stream(),  
[build]                 ^~~~~~~~~~~~~~  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/websocket/impl/read.hpp(78,9): note: in instantiation of member function 'boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>, boost::asio::mutable_buffer>::operator()' requested here  
[build]         (*this)({}, 0, false);  
[build]         ^  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/websocket/impl/read.hpp(797,21): note: in instantiation of function template specialization 'boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>, boost::asio::mutable_buffer>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>>' requested here  
[build]                     read_some_op<read_op, mutable_buffers_type>(  
[build]                     ^  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/websocket/impl/read.hpp(761,9): note: in instantiation of member function 'boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>::operator()' requested here  
[build]         (*this)({}, 0, false);  
[build]         ^  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/websocket/impl/read.hpp(869,9): note: in instantiation of function template specialization 'boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>::read_op<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>>' requested here  
[build]         read_op<  
[build]         ^  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/asio/async_result.hpp(482,5): note: in instantiation of function template specialization 'boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::run_read_op::operator()<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>' requested here  
[build]     BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
[build]     ^  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/asio/detail/config.hpp(135,37): note: expanded from macro 'BOOST_ASIO_MOVE_CAST'  
[build] # define BOOST_ASIO_MOVE_CAST(type) static_cast<type&&>  
[build]                                     ^  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/asio/async_result.hpp(896,21): note: (skipping 4 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
[build]     Signatures...>::initiate(BOOST_ASIO_MOVE_CAST(Initiation)(initiation),  
[build]                     ^  
[build] C:\Src\NetworkService\src\HTTPWSServer.cc(632,9): note: in instantiation of function template specialization 'bda::thrift_websocket_session<bda::ssl_websocket_session>::do_accept<boost::beast::http::basic_string_body<char>, std::allocator<char>>' requested here  
[build]         do_accept(std::move(aHTTPRequest));  
[build]         ^  
[build] C:\Src\NetworkService\src\HTTPWSServer.cc(779,69): note: in instantiation of function template specialization 'bda::thrift_websocket_session<bda::ssl_websocket_session>::run<boost::beast::http::basic_string_body<char>, std::allocator<char>>' requested here  
[build]         std::make_shared<ssl_websocket_session>(std::move(stream))->run(std::move(aHTTPRequest), mProtocolFactory, mProcessor);  
[build]                                                                     ^  
[build] C:\Src\NetworkService\src\HTTPWSServer.cc(835,20): note: in instantiation of function template specialization 'bda::http_session<bda::ssl_http_session>::make_websocket_session<boost::beast::http::basic_string_body<char>, std::allocator<char>>' requested here  
[build]             return make_websocket_session(derived().release_stream(), parser_->release());  
[build]                    ^  
[build] C:\Src\NetworkService\src\HTTPWSServer.cc(811,32): note: in instantiation of member function 'bda::http_session<bda::ssl_http_session>::on_read' requested here  
[build]                 &http_session::on_read,  
[build]                                ^  
[build] C:\Src\NetworkService\src\HTTPWSServer.cc(982,9): note: in instantiation of member function 'bda::http_session<bda::ssl_http_session>::do_read' requested here  
[build]         do_read();  
[build]         ^  
[build] C:\data\usr-dst-C10Sk32c193532215c1600\Debug\include\boost/beast/ssl/ssl_stream.hpp(679,5): note: candidate function [with AsyncStream = boost::beast::basic_stream<boost::asio::ip::tcp>, TeardownHandler = boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (bda::thrift_websocket_session<bda::ssl_websocket_session>::*)(boost::system::error_code, unsigned long long), std::shared_ptr<bda::ssl_websocket_session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>, boost::asio::mutable_buffer>]  
[build]     async_teardown(  
[build]     ^  
```  
  
### Version of Beast  
  
I'm using Beast from official boost 1.81.0.  
  
### Steps necessary to reproduce the problem  
  
The code is pretty similar to `advanced_server_flex` in case that helps?  
  
### All relevant compiler information  
  
The compiler is `clang-cl` from official upstream LLVM, version 16.0.0. It uses Microsoft headers and libraries from Visual Studio 2022 17.5.2.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-03-22 11:20:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1479382796  

Weird, I tested on that compiler version earlier today. It's too late to fix for 1.82, we'll hopefully have something in 1.83.

---

## Comment 2

> Username: emmenlau  
> Created at: 2023-03-22 11:58:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1479424340  

Thanks for the quick response @klemens-morgenstern . Maybe the problem is only in combination with boost 1.81? I did not test 1.82 beta, so keeping fingers crossed that 1.82 final does not have the problem (or do you think it will?).

---

## Comment 3

> Username: emmenlau  
> Created at: 2023-03-28 08:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1486409466  

I just tried to build boost 1.81.0 on Ubuntu 22.04 x86_64 with clang from LLVM 16.0.0 (official upstream Ubuntu packages). The build fails with the same error!  
  
So either my build settings are rigged (which I can not think), or the problem affects clang 16 in general, not just clang-cl!   
  
Help would be greatly appreciated :-(

---

## Comment 4

> Username: vient  
> Created at: 2023-03-29 16:43:50 UTC  
> Updated at: 2023-03-29 17:03:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1488944005  

https://github.com/boostorg/beast/issues/2648 same issue, it is a bug in Clang 16, triggered only with `std=c++20` or `c++2b`

---

## Comment 5

> Username: emmenlau  
> Created at: 2023-03-31 07:40:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1491453396  

Thanks @vient for making the connection!  
  
@klemens-morgenstern , should this issue now be closed in favor of the upstream LLVM issue? There is probably not much boost beast team can do here?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-05-12 14:34:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1545843825  

I'm getting the same error on clang now, and it looks to be a compiler error. I can't find a workaround.  
  
```cpp  
/home/klemens/develop/boost/libs/beast/include/boost/beast/ssl/ssl_stream.hpp:690:5: note: candidate function [with AsyncStream = boost::beast::basic_stream<boost::asio::ip::tcp>, TeardownHandler = boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>, boost::asio::mutable_buffer>]  
    async_teardown(  
    ^  
/home/klemens/develop/boost/libs/beast/include/boost/beast/ssl/ssl_stream.hpp:713:1: note: candidate function [with AsyncStream = boost::beast::basic_stream<boost::asio::ip::tcp>, TeardownHandler = boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session>>, boost::beast::basic_flat_buffer<std::allocator<char>>>, boost::asio::mutable_buffer>]  
```

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-05-12 14:41:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1545853594  

Nevermind, the concept is causing the problem. I think we can somewhat safely disable that. Can you check if #2683 does it for you?

---

## Comment 8

> Username: vient  
> Created at: 2023-05-12 16:25:09 UTC  
> Updated at: 2023-05-12 17:47:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1545995099  

Yeah, I mentioned in https://github.com/boostorg/beast/issues/2648 that we ended up applying essentially the same patch ourselves, it works.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2023-05-15 01:46:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2661#issuecomment-1547079518  

I think just defining `BOOST_ASIO_DISABLE_CONCEPTS` for a compiler bug is better then me adding strange workarounds.
