# #1575 - http::read compilation error with BOOST_BEAST_USE_STD_STRING_VIEW and MSVC [Closed]

> Username: sorf  
> Created at: 2019-04-15 22:55:49 UTC  
> Updated at: 2019-04-19 01:09:58 UTC  
> Closed at: 2019-04-19 01:09:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1575  

### Version of Beast  
  
251  
  
### Steps necessary to reproduce the problem  
  
``` cpp  
//beast_http_read.cpp  
#define BOOST_BEAST_USE_STD_STRING_VIEW  
#include <string>  
#include <string_view>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast.hpp>  
  
namespace net = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
void f() {  
  
    net::io_context io_context;  
    net::ip::tcp::socket socket(io_context);  
      
    beast::flat_buffer buffer;  
    http::response<http::vector_body<char>> response;  
  
    boost::system::error_code ec;  
    http::read(socket, buffer, response, ec);  
}  
```  
compiled as:  
  
```  
cl /c /I<path/to/beast/develop>   /I<path/to/boost/1.70> /D _WIN32_WINNT=0x0A00 /std:c++17 /EHsc beast_http_read.cpp  
```  
### All relevant compiler information  
  
Compilation error output:  
  
```  
cl /c /Id:\Sorin\Work\C++\boost-beast\include   /Ic:\.conan\c1e193\1\include /D _WIN32_WINNT=0x0A00 /std:c++17 /EHsc beast_http_read.cpp  
Microsoft (R) C/C++ Optimizing Compiler Version 19.16.27030.1 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
beast_http_read.cpp  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(802): error C2664: 'bool boost::beast::http::detail::basic_parser_base::parse_dec(const char *,const char *,uint64_t &)': cannot convert argument 1 from 'std::_String_view_iterator<_Traits>' to 'const char *'  
        with  
        [  
            _Traits=std::char_traits<char>  
        ]  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(803): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(748): note: while compiling class template member function 'void boost::beast::http::basic_parser<false>::do_field(boost::beast::http::field,boost::beast::string_view,boost::beast::error_code &)'  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(427): note: see reference to function template instantiation 'void boost::beast::http::basic_parser<false>::do_field(boost::beast::http::field,boost::beast::string_view,boost::beast::error_code &)' being compiled  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(50): note: while compiling class template member function 'boost::optional<uint64_t> boost::beast::http::basic_parser<false>::content_length(void) const'  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(162): note: see reference to function template instantiation 'boost::optional<uint64_t> boost::beast::http::basic_parser<false>::content_length(void) const' being compiled  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(85): note: while compiling class template member function 'size_t boost::beast::http::basic_parser<false>::put(boost::asio::const_buffer,boost::beast::error_code &)'  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.hpp(41): note: see reference to function template instantiation 'size_t boost::beast::http::basic_parser<false>::put(boost::asio::const_buffer,boost::beast::error_code &)' being compiled  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/basic_parser.ipp(226): note: while compiling class template member function 'void boost::beast::http::basic_parser<false>::put_eof(boost::beast::error_code &)'  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/read.hpp(48): note: see reference to function template instantiation 'void boost::beast::http::basic_parser<false>::put_eof(boost::beast::error_code &)' being compiled  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/read.hpp(150): note: see reference to function template instantiation 'size_t boost::beast::http::detail::parse_until<DynamicBuffer,false,boost::beast::http::detail::read_all_condition<false>::()::<lambda_890e201fd67d71e633495113d1f5758c>>(DynamicBuffer &,boost::beast::http::basic_parser<false> &,boost::beast::error_code &,Condition)' being compiled  
        with  
        [  
            DynamicBuffer=boost::beast::flat_buffer,  
            Condition=boost::beast::http::detail::read_all_condition<false>::()::<lambda_890e201fd67d71e633495113d1f5758c>  
        ]  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/core/detail/impl/read.hpp(208): note: see reference to function template instantiation 'size_t boost::beast::http::detail::read_all_condition<false>::operator ()<DynamicBuffer>(boost::beast::error_code &,size_t,DynamicBuffer &)' being compiled  
        with  
        [  
            DynamicBuffer=boost::beast::flat_buffer  
        ]  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/core/detail/impl/read.hpp(208): note: see reference to function template instantiation 'size_t boost::beast::http::detail::read_all_condition<false>::operator ()<DynamicBuffer>(boost::beast::error_code &,size_t,DynamicBuffer &)' being compiled  
        with  
        [  
            DynamicBuffer=boost::beast::flat_buffer  
        ]  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/read.hpp(435): note: see reference to function template instantiation 'size_t boost::beast::detail::read<SyncReadStream,DynamicBuffer,boost::beast::http::detail::read_all_condition<false>,void>(SyncReadStream &,DynamicBuffer &,CompletionCondition,boost::beast::error_code &)' being compiled  
        with  
        [  
            SyncReadStream=boost::asio::ip::tcp::socket,  
            DynamicBuffer=boost::beast::flat_buffer,  
            CompletionCondition=boost::beast::http::detail::read_all_condition<false>  
        ]  
d:\Sorin\Work\C++\boost-beast\include\boost/beast/http/impl/read.hpp(519): note: see reference to function template instantiation 'size_t boost::beast::http::read<SyncReadStream,DynamicBuffer,false>(SyncReadStream &,DynamicBuffer &,boost::beast::http::basic_parser<false> &,boost::beast::error_code &)' being compiled  
        with  
        [  
            SyncReadStream=boost::asio::ip::tcp::socket,  
            DynamicBuffer=boost::beast::flat_buffer  
        ]  
beast_http_read.cpp(22): note: see reference to function template instantiation 'size_t boost::beast::http::read<boost::asio::ip::tcp::socket,boost::beast::flat_buffer,false,boost::beast::http::vector_body<char,std::allocator<char>>,std::allocator<char>>(SyncReadStream &,DynamicBuffer &,boost::beast::http::message<false,boost::beast::http::vector_body<char,std::allocator<char>>,boost::beast::http::fields> &,boost::beast::error_code &)' being compiled  
        with  
        [  
            SyncReadStream=boost::asio::ip::tcp::socket,  
            DynamicBuffer=boost::beast::flat_buffer  
        ]  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-15 23:23:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1575#issuecomment-483454309  

I can reproduce this. The problem is we need to figure out a way to get this into the test matrix without causing Appveyor to time out (it has a 1-hour limit). @djarek ideas?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-04-18 20:35:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1575#issuecomment-484680844  

This should be fixed in v252, I did it a different way (hopefully one that is less error-prone).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-19 01:09:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1575#issuecomment-484736822  

Fixed in v252
