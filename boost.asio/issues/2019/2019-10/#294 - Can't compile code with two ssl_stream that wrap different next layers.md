# #294 - Can't compile code with two ssl_stream that wrap different next layers [Closed]

> Username: Parean  
> Created at: 2019-10-23 13:30:43 UTC  
> Updated at: 2019-10-25 10:30:09 UTC  
> Closed at: 2019-10-25 10:30:09 UTC  
> Url: https://github.com/boostorg/asio/issues/294  

I want to understand why when creating two **ssl_streams** wrapping different objects for the tcp layer, I get the following compilation errors:  
```  
------ Build started: Project: test, Configuration: Debug x64 ------  
>Microsoft (R) C/C++ Optimizing Compiler Version 19.22.27905 for x64  
>Copyright (C) Microsoft Corporation.  All rights reserved.  
>cl /c /I"D:\workspace\openssl-static\openssl-OpenSSL_1_1_1a\openssl-OpenSSL_1_1_1a\include" /ID:\workspace\boost\VS19\boost_1_70_0 /Zi /W3 /WX- /diagnostics:column /Od /Ob0 /D WIN32 /D _WINDOWS /D _WIN32_WINNT=0x0600 /D _SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING /D "CMAKE_INTDIR=\"Debug\"" /D _MBCS /Gm- /EHsc /RTC1 /MTd /GS /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /GR /std:c++17 /Fo"test.dir\Debug\\" /Fd"test.dir\Debug\vc142.pdb" /Gd /TP /errorReport:prompt D:\workspace\main.cpp  
>main.cpp  
>D:\workspace\boost\VS19\boost_1_70_0\boost/beast/ssl/ssl_stream.hpp(655,5): error C2995:  'void boost::beast::teardown(boost::beast::role_type,boost::beast::ssl_stream<NextLayer> &,boost::system::error_code &)': function template has already been defined  
>D:\workspace\boost\VS19\boost_1_70_0\boost/beast/ssl/ssl_stream.hpp(655): message :  see declaration of 'boost::beast::teardown'  
>D:\workspace\main.cpp(12): message :  see reference to class template instantiation 'boost::beast::ssl_stream<boost::asio::ip::tcp::socket>' being compiled  
>D:\workspace\boost\VS19\boost_1_70_0\boost/beast/ssl/ssl_stream.hpp(668,5): error C2995:  'void boost::beast::async_teardown(boost::beast::role_type,boost::beast::ssl_stream<NextLayer> &,TeardownHandler &&)': function template has already been defined  
>D:\workspace\boost\VS19\boost_1_70_0\boost/beast/ssl/ssl_stream.hpp(668): message :  see declaration of 'boost::beast::async_teardown'  
>Done building project "test.vcxproj" -- FAILED.  
```  
  
All errors disappear if both streams have the same template parameter (that is, either both have a **boost::beast::tcp_stream** or **boost::asio::ip::tcp::socket**).   
  
```  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/context.hpp>  
#include <boost/beast/core/tcp_stream.hpp>  
#include <boost/beast/ssl/ssl_stream.hpp>  
  
int main() {  
  boost::asio::io_context ioc;  
  boost::asio::ssl::context ctx(boost::asio::ssl::context::sslv23_client);  
  ctx.set_default_verify_paths();  
  boost::beast::ssl_stream<boost::beast::tcp_stream> stream_1(ioc, ctx);  
  boost::beast::ssl_stream<boost::asio::ip::tcp::socket> stream_2(ioc, ctx);  
}  
```  
  
I want to understand if this is a bug, or desired behavior. If the second, it would be great if someone explained to me why. Also, I would like to better understand in what cases it's worth using the tcp_stream, and in which the socket.

---

## Comment 1

> Username: Parean  
> Created at: 2019-10-25 10:30:09 UTC  
> Url: https://github.com/boostorg/asio/issues/294#issuecomment-546299439  

https://stackoverflow.com/questions/58503432/cant-compile-code-with-two-ssl-stream-that-wrap-different-next-layers/58549246#58549246
