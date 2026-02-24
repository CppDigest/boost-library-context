# #201 - [GCC] UB Sanitizer Runtime error in async_write() [Closed]

> Username: djarek  
> Created at: 2016-11-26 17:32:12 UTC  
> Updated at: 2017-05-09 14:22:27 UTC  
> Closed at: 2017-05-09 14:22:27 UTC  
> Url: https://github.com/boostorg/beast/issues/201  

A runtime error occurs when calling async_write() on ```beast::http::response<beast::http::string_body>```:  
```  
/usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:238:17: runtime error: downcast of address 0x7ffd24313668 which does not point to an object of type 'basic_unlockedbuf'  
0x7ffd24313668: note: object is of type 'std::__cxx11::basic_stringbuf<char, std::char_traits<char>, std::allocator<char> >'  
 ac 7f 00 00  f0 da 79 85 ac 7f 00 00  81 ea 00 00 60 61 00 00  81 ea 00 00 60 61 00 00  81 ea 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'std::__cxx11::basic_stringbuf<char, std::char_traits<char>, std::allocator<char> >'  
```  
Here's a gdb backtrace with the error:  
```  
(gdb) bt  
bt  
#0 boost::detail::lexical_istream_limited_src<char, std::char_traits<char>, true, 2ul>::shl_input_streamable<boost::basic_string_ref<char, std::char_traits<char> > const> (this=0x7fffffffd080, input=...) at /usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:239  
#1 0x0000000000a0471d in boost::detail::lexical_istream_limited_src<char, std::char_traits<char>, true, 2ul>::operator<< <boost::basic_string_ref<char, std::char_traits<char> > > (this=0x7fffffffd080, input=...) at /usr/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:497  
#2 0x00000000009ff586 in boost::detail::lexical_converter_impl<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::basic_string_ref<char, std::char_traits<char> > >::try_convert (arg=..., result="") at /usr/include/boost/lexical_cast/detail/converter_lexical.hpp:476  
#3 0x00000000009f94e1 in boost::conversion::detail::try_lexical_convert<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::basic_string_ref<char, std::char_traits<char> > > (arg=..., result="") at /usr/include/boost/lexical_cast/try_lexical_convert.hpp:173  
#4 0x00000000009f0e6f in boost::lexical_cast<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::basic_string_ref<char, std::char_traits<char> > > (arg=...) at /usr/include/boost/lexical_cast.hpp:41  
#5 0x00000000009e76f3 in beast::detail::write_dynabuf<beast::basic_streambuf<std::allocator<char> >, boost::basic_string_ref<char, std::char_traits<char> > > (dynabuf=..., t=...) at /home/damian/projects/forgottenserver/ext/Beast/include/beast/core/detail/write_dynabuf.hpp:123  
#6 0x00000000009d6225 in beast::write<beast::basic_streambuf<std::allocator<char> >, boost::basic_string_ref<char, std::char_traits<char> > > (dynabuf=...) at /home/damian/projects/forgottenserver/ext/Beast/include/beast/core/write_dynabuf.hpp:58  
#7 0x00000000009d0070 in beast::http::detail::write_fields<beast::basic_streambuf<std::allocator<char> >, beast::http::basic_fields<std::allocator<char> > > (dynabuf=..., fields=...) at /home/damian/projects/forgottenserver/ext/Beast/include/beast/http/impl/write.ipp:86  
#8 0x00000000009c6d4a in beast::http::detail::write_preparation<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > >::init (this=0x612000047f00, ec=...) at /home/damian/projects/forgottenserver/ext/Beast/include/beast/http/impl/write.ipp:295  
#9 0x00000000009516cf in beast::http::detail::write_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, http::Peer::write()::<lambda(http::ErrorCode)>, boost::asio::detail::is_continuation_if_running>, false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > >::operator()(beast::error_code, std::size_t, bool) (this=0x7fffffffd790, ec=..., again=false) at /home/damian/projects/forgottenserver/ext/Beast/include/beast/http/impl/write.ipp:467  
#10 0x000000000094e8b2 in beast::http::detail::write_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> >, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, http::Peer::write()::<lambda(http::ErrorCode)>, boost::asio::detail::is_continuation_if_running>, false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > >::write_op<boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, http::Peer::write()::<lambda(http::ErrorCode)>, boost::asio::detail::is_continuation_if_running>&, const beast::http::message<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > >&>(boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, http::Peer::write()::<lambda(http::ErrorCode)>, boost::asio::detail::is_continuation_if_running> &, boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &) (this=0x7fffffffd790, h=..., s=...) at /home/damian/projects/forgottenserver/ext/Beast/include/beast/http/impl/write.ipp:409  
#11 0x000000000094c949 in beast::http::async_write<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> >, boost::asio::detail::wrapped_handler<boost::asio::io_service::strand, http::Peer::write()::<lambda(http::ErrorCode)>, boost::asio::detail::is_continuation_if_running> >(boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &, const beast::http::message<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > &, <unknown type in /home/damian/projects/forgottenserver/build/tfs, CU 0xa7d3d, DIE 0xfeda2>) (stream=..., msg=..., handler=<unknown type in /home/damian/projects/forgottenserver/build/tfs, CU 0xa7d3d, DIE 0xfeda2>) at /home/damian/projects/forgottenserver/ext/Beast/include/beast/http/impl/write.ipp:751  
#12 0x000000000094a7fd in http::Peer::write (this=0x61600000f390) at /home/damian/projects/forgottenserver/src/api_server/peer.cpp:99  
#13 0x00000000009a066d in http::Functor::operator() (this=0x7fffffffdba0) at /home/damian/projects/forgottenserver/src/api_server/peer.cpp:181  
#14 0x00000000009cb1f8 in boost::asio::asio_handler_invoke<http::Functor> (function=...) at /usr/include/boost/asio/handler_invoke_hook.hpp:69  
#15 0x00000000009c0a67 in boost_asio_handler_invoke_helpers::invoke<http::Functor, http::Functor> (function=..., context=...) at /usr/include/boost/asio/detail/handler_invoke_helpers.hpp:37  
#16 0x00000000009c0e87 in boost::asio::detail::completion_handler<http::Functor>::do_complete (owner=0x611000009f00, base=0x610000625540) at /usr/include/boost/asio/detail/completion_handler.hpp:68  
#17 0x0000000000990cf8 in boost::asio::detail::task_io_service_operation::complete (this=0x610000625540, owner=..., ec=..., bytes_transferred=0) at /usr/include/boost/asio/detail/task_io_service_operation.hpp:38  
#18 0x000000000099b9fd in boost::asio::detail::strand_service::do_complete (owner=0x611000009f00, base=0x60b00000ad80, ec=...) at /usr/include/boost/asio/detail/impl/strand_service.ipp:167  
#19 0x0000000000990cf8 in boost::asio::detail::task_io_service_operation::complete (this=0x60b00000ad80, owner=..., ec=..., bytes_transferred=0) at /usr/include/boost/asio/detail/task_io_service_operation.hpp:38  
#20 0x00000000013b3d14 in boost::asio::detail::task_io_service::do_run_one (this=0x611000009f00, lock=..., this_thread=..., ec=...) at /usr/include/boost/asio/detail/impl/task_io_service.ipp:372  
#21 0x00000000013b3527 in boost::asio::detail::task_io_service::run (this=0x611000009f00, ec=...) at /usr/include/boost/asio/detail/impl/task_io_service.ipp:149  
#22 0x00000000013b412d in boost::asio::io_service::run (this=0x7fffffffe1e0) at /usr/include/boost/asio/impl/io_service.ipp:59  
#23 0x00000000013ad45f in ServiceManager::run (this=0x7fffffffe2c0) at /home/damian/projects/forgottenserver/src/server.cpp:52  
#24 0x000000000115dd05 in main (argc=1, argv=0x7fffffffe598) at /home/damian/projects/forgottenserver/src/otserv.cpp:89  
```  
  
The issue always reproduces with gcc 5.2.1, Ubuntu 16.04. Beast version: [b21](https://github.com/vinniefalco/Beast/tree/b21). Compiled with ```-fsanitize=address,undefined```.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-26 21:07:54 UTC  
> Url: https://github.com/boostorg/beast/issues/201#issuecomment-263086051  

That's a known bug in glib++ and not an actual problem in practice. You should add it to the blacklist file. They also come up in the UBSan Travis build for Beast:  
https://travis-ci.org/vinniefalco/Beast/jobs/177454924  
  
I'll mark this issue as a Bug and try to fix the blacklist file for CI.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-05 02:44:27 UTC  
> Url: https://github.com/boostorg/beast/issues/201#issuecomment-299358065  

Blacklist is fixed as of b38

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-09 14:22:27 UTC  
> Url: https://github.com/boostorg/beast/issues/201#issuecomment-300180678  

Fixed in this commit  
https://github.com/vinniefalco/Beast/commit/bcc6ad8e5ea828189910aff91dfdafd733603fb2
