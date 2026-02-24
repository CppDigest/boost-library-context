# #1160 - Memory Exception unhandled by asio [Closed]

> Username: ngohr  
> Created at: 2018-06-13 13:24:13 UTC  
> Updated at: 2018-11-28 21:44:26 UTC  
> Closed at: 2018-11-28 21:44:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1160  

advanced_server_flex.cpp Example,  
does not handle allready freed streams and it seems that asio cannot handle this:  
  
After Timeout of a wss connection within do_eof the memory was freed before async_shutdown().  
This causes segementation fault, after connecting with wss... send a string and wait...  
Here my valgrind output:  
  
==10243== Invalid read of size 8  
==10243==    at 0x4165BD: boost::asio::ssl::detail::engine::perform(int (boost::asio::ssl::detail::engine::*)(void*, unsigned long), void*, unsigned long, boost::system::error_code&, unsigned long*) (engine.ipp:233)  
==10243==    by 0x4161FA: boost::asio::ssl::detail::engine::shutdown(boost::system::error_code&) (engine.ipp:141)  
==10243==    by 0x416CEB: boost::asio::ssl::detail::shutdown_op::operator()(boost::asio::ssl::detail::engine&, boost::system::error_code&, unsigned long&) const (shutdown_op.hpp:37)  
==10243==    by 0x455220: boost::asio::ssl::detail::io_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::asio::ssl::detail::shutdown_op, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >::operator()(boost::system::error_code, unsigned long, int) (io.hpp:136)  
==10243==    by 0x449B3D: void boost::asio::ssl::detail::async_io<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::asio::ssl::detail::shutdown_op, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >(boost::asio::basic_stream_socket<boost::asio::ip::tcp>&, boost::asio::ssl::detail::stream_core&, boost::asio::ssl::detail::shutdown_op const&, boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> >&) (io.hpp:333)  
==10243==    by 0x43C33E: boost::asio::async_result<std::decay<boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >::type, void (boost::system::error_code)>::return_type boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::async_shutdown<boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >(boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> >&&) (stream.hpp:563)  
==10243==    by 0x42FA0B: boost::asio::async_result<std::decay<boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >::type, void (boost::system::error_code)>::return_type ssl_stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::async_shutdown<boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> > >(boost::asio::executor_binder<std::_Bind<std::_Mem_fn<void (ssl_http_session::*)(boost::system::error_code)> (std::shared_ptr<ssl_http_session>, std::_Placeholder<1>)>, boost::asio::strand<boost::asio::io_context::executor_type> >&&) (ssl_stream.hpp:227)  
==10243==    by 0x421B72: ssl_http_session::do_eof() (advancedserver_flex.cpp:1052)  
==10243==    by 0x421CCA: ssl_http_session::do_timeout() (advancedserver_flex.cpp:1079)  
==10243==    by 0x42F310: http_session<ssl_http_session>::on_timer(boost::system::error_code) (advancedserver_flex.cpp:816)  
==10243==    by 0x51A967: void std::_Mem_fn<void (http_session<ssl_http_session>::*)(boost::system::error_code)>::_M_call<std::shared_ptr<ssl_http_session>&, boost::system::error_code const&>(std::shared_ptr<ssl_http_session>&, void const volatile*, boost::system::error_code const&) const (in ./advancedServer)  
==10243==    by 0x509113: void std::_Mem_fn<void (http_session<ssl_http_session>::*)(boost::system::error_code)>::operator()<std::shared_ptr<ssl_http_session>&, boost::system::error_code const&, void>(std::shared_ptr<ssl_http_session>&, boost::system::error_code const&) const (functional:578)  
==10243==  Address 0x28 is not stack'd, malloc'd or (recently) free'd  
==10243==  
==10243==  
==10243== Process terminating with default action of signal 11 (SIGSEGV)  
==10243==  Access not within mapped region at address 0x28

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-13 13:49:45 UTC  
> Updated at: 2018-06-13 13:50:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1160#issuecomment-396944043  

Yeah...the timeout code in the advanced servers has a bug. It will be fixed in Boost 1.68.0. See: https://github.com/boostorg/beast/issues/1091

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-07-13 14:46:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1160#issuecomment-404855107  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: aaron-michaux  
> Created at: 2018-10-18 11:45:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1160#issuecomment-430977524  

> Yeah...the timeout code in the advanced servers has a bug. It will be fixed in Boost 1.68.0. See: #1091  
  
I'm using Boost 1.68.0, and I believe that the bug is still there.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-10-18 11:48:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1160#issuecomment-430978241  

Another fix went in after 1.68, can you please try the tip of the **master** branch? This is the fix:  
https://github.com/vinniefalco/beast/commit/2c8873d4f6b29f8a9e1a92361a99dedffb1ca47b

---

## Comment 5

> Username: aaron-michaux  
> Created at: 2018-10-18 12:26:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1160#issuecomment-430988798  

Hi, thanks, this works. The `ssl_websocket_session::on_shutdown` method still reports a `Bad file descriptor` error (a different problem). Not sure if this is indicative of some deeper problem with `boost::beast::ssl_stream<tcp::socket>`.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-10-18 13:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1160#issuecomment-431015185  

It could be that the fix is an improvement but not perfect.

---

## Comment 7

> Username: ngohr  
> Created at: 2018-10-18 14:53:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1160#issuecomment-431040348  

Was gone in my reposetories, ive choosed another solution. Sorry  
  
Vinnie Falco <notifications@github.com> schrieb am Do., 18. Okt. 2018,  
15:48:  
  
> It could be that the fix is an improvement but not perfect.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1160#issuecomment-431015185>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AJHMtgIYSAlc7MVXu8MZA10Y4XFd0sfTks5umIaQgaJpZM4UmMK0>  
> .  
>
