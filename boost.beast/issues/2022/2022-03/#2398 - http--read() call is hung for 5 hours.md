# #2398 - http::read() call is hung for 5 hours [Closed]

> Username: GaneshTambat1  
> Created at: 2022-03-16 11:23:13 UTC  
> Updated at: 2022-06-14 17:36:27 UTC  
> Closed at: 2022-06-14 17:36:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2398  

Seeing this issue intermittently. I understand sync calls do not have timeouts but shouldn't such calls report an error and come out if there is an issue on the network? Is there any workaround for such a hang issue? Please let me know.  
  
Relevant call stack:  
  
00 0000007b`8b3f9558 00007ffe`4a0d8033 ntdll!NtWaitForSingleObject+0x14  
01 0000007b`8b3f9560 00007ffe`4a0d28d2 mswsock!SockWaitForSingleObject+0x133  
02 0000007b`8b3f9600 00007ffe`4e31bf21 mswsock!WSPRecv+0x5d2  
03 0000007b`8b3f9700 00007ffe`1a0f8cb6 ws2_32!WSARecv+0x171  
04 0000007b`8b3f9780 00007ffe`1a0f9bc1 plugin!boost::asio::detail::socket_ops::recv+0x66 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\asio\detail\impl\socket_ops.ipp @ 768]   
05 0000007b`8b3f97f0 00007ffe`1a0ee88f plugin!boost::asio::detail::socket_ops::sync_recv+0xe1 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\asio\detail\impl\socket_ops.ipp @ 814]   
06 (Inline Function) --------`-------- plugin!boost::asio::detail::win_iocp_socket_service_base::receive+0x53 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\asio\detail\win_iocp_socket_service_base.hpp @ 325]   
07 (Inline Function) --------`-------- plugin!boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>::read_some+0x53 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\asio\basic_stream_socket.hpp @ 920]   
08 (Inline Function) --------`-------- plugin!boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::read_some+0x53 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\beast\core\basic_stream.hpp @ 1269]   
09 0000007b`8b3f9ad0 00007ffe`1a0f2f60 plugin!boost::asio::ssl::detail::io<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::read_op<boost::asio::mutable_buffer> >+0x1ef [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\asio\ssl\detail\io.hpp @ 45]   
0a (Inline Function) --------`-------- plugin!boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy> >::read_some+0x1d [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\asio\ssl\stream.hpp @ 671]   
0b (Inline Function) --------`-------- plugin!boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy> > >::read_some+0x20 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\beast\core\impl\flat_stream.hpp @ 141]   
0c (Inline Function) --------`-------- plugin!boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy> >::read_some+0x20 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\beast\ssl\ssl_stream.hpp @ 613]   
0d (Inline Function) --------`-------- plugin!boost::beast::detail::read+0xe3 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\beast\core\detail\impl\read.hpp @ 205]   
0e (Inline Function) --------`-------- plugin!boost::beast::http::read+0xf1 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\beast\http\impl\read.hpp @ 437]   
0f 0000007b`8b3f9bb0 00007ffe`1a0f7a7b plugin!boost::beast::http::read<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy> >,boost::beast::basic_flat_buffer<std::allocator<char> >,0,boost::beast::http::basic_string_body<char,std::char_traits<char>,std::allocator<char> >,std::allocator<char> >+0x1c0 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\beast\http\impl\read.hpp @ 522]   
10 (Inline Function) --------`-------- plugin!boost::beast::http::read+0x35 [R:\scf_1.0\PROD\104\nh\sdks\boost-v1.73.0\Win_VS2019_x64\include\boost-1_73\boost\beast\http\impl\read.hpp @ 492]   
11 0000007b`8b3f9e50 00007ffe`19fefbe8 plugin!HttpsRequest::Send+0x143b [R:\scf_1.0\PROD\104\nh\src\scf\common\http_helper\https_request.cpp @ 141]   
12 0000007b`8b3fb270 00007ffe`19fee4ef plugin!OAuthImpl::RequestToken+0x948 [R:\scf_1.0\PROD\104\nh\src\scf\plugins\o365\auth\OAuthImpl.cpp @ 367]

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-03-16 11:29:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2398#issuecomment-1069025160  

> shouldn't such calls report an error and come out if there is an issue on the network?  
  
It depends what the issue is. If the network adapter itself failed or the cable was unplugged, I might expect that event to propagate as a read error (but this would be strictly platform-dependent).  
  
If keepalives were enabled on the TCP connection, I would expect a timeout signal eventually. However TCP can be configured to wait "for ever". Without details on your configuration it's difficult to comment.  
  
In general if you are interested in timeouts, you might want consider either:  
- Using the async interface, or  
- Run a timeout in a parallel thread and on a timeout, close the underlying socket.

---

## Comment 2

> Username: GaneshTambat1  
> Created at: 2022-03-16 13:08:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2398#issuecomment-1069108661  

Thanks. We create a sync HTTP connection with ssl_stream with default values on windows. Does boost set any values for wait or is it dependent on OS?

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-03-16 13:38:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2398#issuecomment-1069141668  

We rely on the underlying stream type, which I expect is ASIO. That in turn relies on openssl by default, which I presume relies on the default underlying implementation (winsock2?)  
  
Once you have created the underlying transport you can use the next_layer() methods to get access to the underlying socket so that you can set options on it.

---

## Comment 4

> Username: GaneshTambat1  
> Created at: 2022-03-28 10:51:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2398#issuecomment-1080496514  

There are methods like "expires_after()". Do these apply only to async calls or can be applied to sync calls as well?  
  
E.g. Can I do something like "beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));" for my sync connection?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-03-28 11:40:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2398#issuecomment-1080545564  

> E.g. Can I do something like "beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));" for my sync connection?  
  
No

---

## Comment 6

> Username: GaneshTambat1  
> Created at: 2022-03-28 16:37:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2398#issuecomment-1080887275  

Thanks.  
  
I think something like this will help me configure timeout for my sync calls:  
  
boost::system::error_code ec;  
boost::asio::socket_base::keep_alive option(true);          
beast::get_lowest_layer(stream).socket().set_option(option, ec);  
  
I am not seeing options to configure keepalive settings so I assume default settings of the OS will be used.
