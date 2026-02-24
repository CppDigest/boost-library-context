# #2906 - Calling the destructor of a websocket stream throws system error 22 (EINVAL) [Closed]

> Username: MeanSquaredError  
> Created at: 2024-07-22 14:51:10 UTC  
> Updated at: 2024-07-22 17:26:02 UTC  
> Closed at: 2024-07-22 17:22:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2906  

Hello,  
I have a custom STOMP implementation that uses an underlying stream of type  
```  
boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>  
```  
  
 After connecting the STOMP implementation reads from/writes to the websocket and then closes the STOMP connection by sending the STOMP command `DISCONNECT`. Then the STOMP implementation reads from the socket waiting for the remote command `RECEIPT`. However it seems that the remote side just closes the websocket without sending the `RECEIPT`, so at one point the websocket read throws a boost system error with error code `boost::beast::websocket::error::closed`  
  
This is a GDB print of the exception is as follows  
```  
(gdb) p e  
$5 = (const boost::system::system_error &) @0x7fffffffd500: {<std::runtime_error> = {<std::exception> = {_vptr.exception = 0x6c69b8 <vtable for boost::system::system_error+16>},   
    _M_msg = "The WebSocket stream was gracefully closed at both endpoints [boost.beast.websocket:1 at /usr/include/boost/beast/websocket/impl/stream.hpp:365:9 in function 'void boost::beast::websocket::stream< <te"...}, code_ = {{  
      d1_ = {val_ = 1, cat_ = 0x80f400 <boost::beast::websocket::make_error_code(boost::beast::websocket::error)::cat>}, d2_ = "\001\000\000\000\377\177\000\000\000\364\200\000\000\000\000"}, lc_flags_ = 7379585}}  
```  
  
After that point attempt to call   
```  
close (boost::beast::websocket::close_code::normal)  
```   
on the websocket throw `boost::system::errc::operation_canceled`  
  
Regardless of whether `close` has been called on the socket, any following attempt to call the destructor of the websocket cause it to throw ```boost::system::errc::invalid_argument```  
  
This is the relevant part of the GDB backtrace  
```  
(gdb) bt  
#0  __pthread_kill_implementation (threadid=<optimized out>, signo=signo@entry=6, no_tid=no_tid@entry=0) at pthread_kill.c:44  
#1  0x00007ffff72a8513 in __pthread_kill_internal (threadid=<optimized out>, signo=6) at pthread_kill.c:78  
#2  0x00007ffff724fc4e in __GI_raise (sig=sig@entry=6) at ../sysdeps/posix/raise.c:26  
#3  0x00007ffff7237902 in __GI_abort () at abort.c:79  
#4  0x00007ffff74a5da9 in __gnu_cxx::__verbose_terminate_handler () at ../../../../libstdc++-v3/libsupc++/vterminate.cc:95  
#5  0x00007ffff74b7c4c in __cxxabiv1::__terminate (handler=<optimized out>) at ../../../../libstdc++-v3/libsupc++/eh_terminate.cc:48  
#6  0x00007ffff74a5849 in __cxa_call_terminate (ue_header_in=0xa37c70) at ../../../../libstdc++-v3/libsupc++/eh_call.cc:56  
#7  0x00007ffff74b74b0 in __cxxabiv1::__gxx_personality_v0 (version=<optimized out>, actions=6, exception_class=5138137972254386944, ue_header=0xa37c70, context=0x7fffffffd150)  
    at ../../../../libstdc++-v3/libsupc++/eh_personality.cc:692  
#8  0x00007ffff7cf4b59 in _Unwind_RaiseException_Phase2 (exc=exc@entry=0xa37c70, context=context@entry=0x7fffffffd150, frames_p=frames_p@entry=0x7fffffffd240) at ../../../libgcc/unwind.inc:64  
#9  0x00007ffff7cf5231 in _Unwind_RaiseException (exc=0xa37c70) at ../../../libgcc/unwind.inc:136  
#10 0x00007ffff74b7ecb in __cxxabiv1::__cxa_throw (obj=<optimized out>, tinfo=0x7ffff7655378 <typeinfo for std::system_error>, dest=0x7ffff74e7270 <std::system_error::~system_error()>)  
    at ../../../../libstdc++-v3/libsupc++/eh_throw.cc:93  
#11 0x00007ffff74a9319 in std::__throw_system_error (__i=22) at /usr/src/debug/gcc-14.1.1-7.fc40.x86_64/obj-x86_64-redhat-linux/x86_64-redhat-linux/libstdc++-v3/include/system_error:233  
#12 0x0000000000441d39 in std::mutex::lock (this=0xa35e08) at /usr/include/c++/14/bits/std_mutex.h:117  
#13 0x000000000044869a in std::lock_guard<std::mutex>::lock_guard (this=0x7fffffffd460, __m=...) at /usr/include/c++/14/bits/std_mutex.h:250  
#14 0x000000000048b1d6 in boost::beast::websocket::detail::service::impl_type::remove (this=0xa38680) at /usr/include/boost/beast/websocket/detail/service.ipp:35  
#15 0x000000000048c2c2 in boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true>::~stream (this=0xa37900,   
    __in_chrg=<optimized out>) at /usr/include/boost/beast/websocket/impl/stream.hpp:47  
#16 0x000000000048c01e in std::default_delete<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true> >::operator() (this=0x824138, __ptr=0xa37900) at /usr/include/c++/14/bits/unique_ptr.h:93  
#17 0x00000000005b9348 in std::__uniq_ptr_impl<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true>, std::default_delete<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true> > >::reset (this=0x824138, __p=0x0)  
    at /usr/include/c++/14/bits/unique_ptr.h:205  
#18 0x00000000005b9be5 in std::unique_ptr<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true>, std::default_delete<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true> > >::reset (this=0x824138, __p=0x0)  
    at /usr/include/c++/14/bits/unique_ptr.h:503  
#19 0x00000000005b74df in std::unique_ptr<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true>, std::default_delete<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, true> > >::operator=(decltype(nullptr)) (  
    this=0x824138) at /usr/include/c++/14/bits/unique_ptr.h:436  
```  
  
It seems that the exception is thrown when the websocket tries to use an invalid mutex, one which is already destroyed or never initialized.  
  
My GCC version  
```  
root@fedora:~# g++ --version  
g++ (GCC) 14.1.1 20240701 (Red Hat 14.1.1-7)  
Copyright (C) 2024 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
My Boost version is 1.83  
```  
root@fedora:~# rpm -q boost-devel  
boost-devel-1.83.0-5.fc40.x86_64  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-22 17:08:33 UTC  
> Updated at: 2024-07-22 17:08:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2906#issuecomment-2243430355  

Could you provide a minimal reproducible example?  
This looks like a concurrency bug. Are you using a multi-threaded executor? Are you passing a concurrency hint to the `io_context` constructor?

---

## Comment 2

> Username: MeanSquaredError  
> Created at: 2024-07-22 17:26:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2906#issuecomment-2243459230  

Everything is single-threaded. I decided to write my own implementation of websocket, so I close the bugreport.
