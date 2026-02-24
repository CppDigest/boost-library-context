# #2021 - http_client_sync.cpp from Boost 1.74 beta failed to build on CentOS 7 [Closed]

> Username: cheese  
> Created at: 2020-07-18 04:12:29 UTC  
> Updated at: 2020-11-16 03:45:56 UTC  
> Closed at: 2020-11-16 03:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2021  

### Version of Beast  
Boost 1.74 beta  
BOOST_BEAST_VERSION 299  
  
### Steps necessary to reproduce the problem  
  
Build http_client_sync.cpp on CentOS 7 with gcc-4.8.5-4.el7.x86_64  
  
### All relevant compiler information  
  
Compiler output ( testbeast.cpp is just a renaming of http_client_sync.cpp ).  
```  
/bin/c++    -I/home/builder/xxx/boost_1_74_0  -Wall -Wno-write-strings -std=gnu++11 -pthread -DVERSION=\"\"  -O2 -g -DNDEBUG   -o CMakeFiles/testbeast.dir/testbeast.cpp.o -c /home/builder/xxx/testbeast.cpp  
In file included from /home/builder/xxx/boost_1_74_0/boost/beast/http.hpp:17:0,  
                 from /home/builder/xxx/testbeast.cpp:19:  
/home/builder/xxx/boost_1_74_0/boost/beast/http/basic_parser.hpp: In instantiation of ‘boost::beast::http::parser<isRequest, Body, Fields>::parser(Arg1&&, ArgN&& ...) [with Arg1 = boost::beast::http::message<false, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::allocator<char> > >, boost::beast::http::basic_fields<std::allocator<char> > >; ArgN = {}; <template-parameter-2-3> = void; bool isRequest = false; Body = boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::allocator<char> > >; Allocator = std::allocator<char>]’:  
/home/builder/xxx/boost_1_74_0/boost/beast/http/impl/read.hpp:642:56:   required from ‘std::size_t boost::beast::http::read(SyncReadStream&, DynamicBuffer&, boost::beast::http::message<isRequest, Body, boost::beast::http::basic_fields<Allocator> >&, boost::beast::error_code&) [with SyncReadStream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >, boost::beast::unlimited_rate_policy>; DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char> >; bool isRequest = false; Body = boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::allocator<char> > >; Allocator = std::allocator<char>; std::size_t = long unsigned int; boost::beast::error_code = boost::system::error_code]’  
/home/builder/xxx/boost_1_74_0/boost/beast/http/impl/read.hpp:615:43:   required from ‘std::size_t boost::beast::http::read(SyncReadStream&, DynamicBuffer&, boost::beast::http::message<isRequest, Body, boost::beast::http::basic_fields<Allocator> >&) [with SyncReadStream = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >, boost::beast::unlimited_rate_policy>; DynamicBuffer = boost::beast::basic_flat_buffer<std::allocator<char> >; bool isRequest = false; Body = boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::allocator<char> > >; Allocator = std::allocator<char>; std::size_t = long unsigned int]’  
/home/builder/xxx/testbeast.cpp:80:39:   required from here  
/home/builder/xxx/boost_1_74_0/boost/beast/http/basic_parser.hpp:74:44: error: conversion from ‘uint64_t {aka long unsigned int}’ to non-scalar type ‘boost::optional<long unsigned int>’ requested  
             default_body_limit(is_request{});   // max payload body  
                                            ^  
In file included from /home/builder/xxx/boost_1_74_0/boost/beast/http/parser.hpp:503:0,  
                 from /home/builder/xxx/boost_1_74_0/boost/beast/http.hpp:27,  
                 from /home/builder/xxx/testbeast.cpp:19:  
/home/builder/xxx/boost_1_74_0/boost/beast/http/impl/parser.hpp:34:31: note: synthesized method ‘boost::beast::http::basic_parser<isRequest>::basic_parser() [with bool isRequest = false]’ first required here   
     , rd_(m_.base(), m_.body())  
                               ^  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-18 04:53:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2021#issuecomment-660425424  

Interesting. Thank you. I’ll look into it.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-08-19 07:31:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2021#issuecomment-675904291  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-19 13:15:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2021#issuecomment-676337890  

keepalive

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-09-20 03:28:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2021#issuecomment-695613658  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-09-20 19:57:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2021#issuecomment-695829137  

Keepalive

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2021#issuecomment-720048926  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2021#issuecomment-727717739  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
