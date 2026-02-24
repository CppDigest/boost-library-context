# #2874 - coredump about http BIO_write [Closed]

> Username: ahao1995  
> Created at: 2024-05-29 01:07:04 UTC  
> Updated at: 2024-06-06 01:09:31 UTC  
> Closed at: 2024-06-06 01:09:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2874  

I meet a coredump, I dont know what happend, is there some suggestions to solve this problem, please give me some clues, thanks  
```  
#0  0x00007ff9500000f0 in ?? ()  
#1  0x00007ff9729c381e in bio_write_intern () from /lib64/libcrypto.so.1.1  
#2  0x00007ff9729c3ce7 in BIO_write () from /lib64/libcrypto.so.1.1  
#3  0x00007ff97247ce5f in boost::asio::ssl::detail::engine::put_input (data=..., this=<optimized out>, this=<optimized out>) at /usr/local/include/boost/asio/ssl/detail/impl/engine.ipp:229  
#4  0x00007ff9724f5678 in boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::read_op<boost::asio::mutable_buffer>, boost::asio::detail::composed_op<boost::beast::http::detail::read_some_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::beast::http::detail::read_msg_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (cpp_frame::http::http_connection::*)(boost::system::error_code, unsigned long), cpp_frame::http::http_connection*> >, void (boost::system::error_code, unsigned long)>, void (boost::system::error_code, unsigned long)> >::operator()(boost::system::error_code, unsigned long, int) (this=0x7ff965221388, ec=..., bytes_transferred=<optimized out>, start=0) at /usr/local/include/boost/asio/ssl/detail/io.hpp:269  
#5  0x00007ff9724f8d36 in boost::beast::async_base<boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::read_op<boost::asio::mutable_buffer>, boost::asio::detail::composed_op<boost::beast::http::detail::read_some_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::beast::http::detail::read_msg_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (cpp_frame::http::http_connection::*)(boost::system::error_code, unsigned long), cpp_frame::http::http_connection*> >, void (boost::system::error_code, unsigned long)>, void (boost::system::error_code, unsigned long)> >, boost::asio::any_io_executor, std::allocator<void> >::complete_now<boost::system::error_code&, unsigned long&>(boost::system::error_code&, unsigned long&) (this=0x7ff965221380)  
    at /usr/local/include/boost/beast/core/async_base.hpp:392  
#6  boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::read_op<boost::asio::mutable_buffer>, boost::asio::detail::composed_op<boost::beast::http::detail::read_some_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::beast::http::detail::read_msg_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (cpp_frame::http::http_connection::*)(boost::system::error_code, unsigned long), cpp_frame::http::http_connection*> >, void (boost::system::error_code, unsigned long)>, void (boost::system::error_code, unsigned long)> > >::operator()(boost::system::error_code, unsigned long) (this=this@entry=0x7ff965221380, ec=...,   
    bytes_transferred=0) at /usr/local/include/boost/beast/core/impl/basic_stream.hpp:424  
  
......  
#30 boost::asio::detail::reactive_socket_recv_op<boost::beast::buffers_prefix_view<boost::asio::mutable_buffers_1>, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::read_op<boost::asio::mutable_buffer>, boost::asio::detail::composed_op<boost::beast::http::detail::read_some_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::beast::http::detail::read_msg_op<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (cpp_frame::http::http_connection::*)(boost::system::error_code, unsigned long), cpp_frame::http::http_connection*> >, void (boost::system::error_code, unsigned long)>, void (boost::system::error_code, unsigned long)> > >, boost::asio::any_io_executor>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) (owner=0x17033a0, base=<optimized out>)  
    at /usr/local/include/boost/asio/detail/reactive_socket_recv_op.hpp:147  
#31 0x000000000043a9cc in boost::asio::detail::scheduler_operation::complete (bytes_transferred=0, ec=..., owner=0x17033a0, this=0x7ff95018eb50) at /usr/local/include/boost/asio/detail/scheduler_operation.hpp:40  
#32 boost::asio::detail::scheduler::do_run_one (ec=..., this_thread=..., lock=..., this=0x17033a0) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:492  
#33 boost::asio::detail::scheduler::run (this=0x17033a0, ec=...) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:210  
#34 0x00000000004308e6 in boost::asio::io_context::run (this=<optimized out>, this=<optimized out>) at /usr/local/include/boost/asio/impl/io_context.ipp:63  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-05-29 06:13:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2874#issuecomment-2136597038  

Are you using a multi-threaded executor? There might be a concurrency bug in your code. It is hard to tell what's wrong solely from that stack trace; a minimal example would help.

---

## Comment 2

> Username: ahao1995  
> Created at: 2024-05-29 08:03:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2874#issuecomment-2136791095  

thanks, I will try to review my code, there is 2 thread in my code maybe is that bug

---

## Comment 3

> Username: ahao1995  
> Created at: 2024-06-03 01:51:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2874#issuecomment-2144139406  

there are many pthread_cond_wait in stack, I dont know pthread_cond_wait from where, I only use a thead with io_context  
```  
Thread 5 (Thread 0x7f26ea951700 (LWP 1010747) "td_proxy"):  
#0  0x00007f26fa1dd3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
#1  0x000000000043a7dc in boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=..., this=<optimized out>) at /usr/local/include/boost/asio/detail/conditionally_enabled_mutex.hpp:98  
#2  boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=..., this=0x162e310) at /usr/local/include/boost/asio/detail/posix_event.hpp:113  
#3  boost::asio::detail::conditionally_enabled_event::wait (lock=..., this=0x162e308) at /usr/local/include/boost/asio/detail/conditionally_enabled_event.hpp:97  
#4  boost::asio::detail::scheduler::do_run_one (ec=..., this_thread=..., lock=..., this=0x162e2a0) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:501  
#5  boost::asio::detail::scheduler::run (this=0x162e2a0, ec=...) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:210  
#6  0x00007f26f94bf9d9 in boost::asio::system_context::thread_function::operator() (this=<optimized out>) at /usr/local/include/boost/asio/impl/system_context.ipp:37  
#7  boost::asio::detail::posix_thread::func<boost::asio::system_context::thread_function>::run (this=<optimized out>) at /usr/local/include/boost/asio/detail/posix_thread.hpp:86  
#8  0x0000000000435510 in boost::asio::detail::boost_asio_detail_posix_thread_function (arg=0x1612120) at /usr/local/include/boost/asio/detail/impl/posix_thread.ipp:74  
#9  0x00007f26fa1d717a in start_thread () from /lib64/libpthread.so.0  
#10 0x00007f26f852ddc3 in clone () from /lib64/libc.so.6  
Thread 4 (Thread 0x7f26eb152700 (LWP 1010746) "td_proxy"):  
#0  0x00007f26fa1dd3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
#1  0x000000000043a7dc in boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=..., this=<optimized out>) at /usr/local/include/boost/asio/detail/conditionally_enabled_mutex.hpp:98  
#2  boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=..., this=0x162e310) at /usr/local/include/boost/asio/detail/posix_event.hpp:113  
#3  boost::asio::detail::conditionally_enabled_event::wait (lock=..., this=0x162e308) at /usr/local/include/boost/asio/detail/conditionally_enabled_event.hpp:97  
#4  boost::asio::detail::scheduler::do_run_one (ec=..., this_thread=..., lock=..., this=0x162e2a0) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:501  
#5  boost::asio::detail::scheduler::run (this=0x162e2a0, ec=...) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:210  
#6  0x00007f26f94bf9d9 in boost::asio::system_context::thread_function::operator() (this=<optimized out>) at /usr/local/include/boost/asio/impl/system_context.ipp:37  
#7  boost::asio::detail::posix_thread::func<boost::asio::system_context::thread_function>::run (this=<optimized out>) at /usr/local/include/boost/asio/detail/posix_thread.hpp:86  
#8  0x0000000000435510 in boost::asio::detail::boost_asio_detail_posix_thread_function (arg=0x15a8610) at /usr/local/include/boost/asio/detail/impl/posix_thread.ipp:74  
#9  0x00007f26fa1d717a in start_thread () from /lib64/libpthread.so.0  
#10 0x00007f26f852ddc3 in clone () from /lib64/libc.so.6  
Thread 3 (Thread 0x7f26eb953700 (LWP 1010745) "td_proxy"):  
#0  0x00007f26fa1dd3fc in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
#1  0x000000000043a7dc in boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=..., this=<optimized out>) at /usr/local/include/boost/asio/detail/conditionally_enabled_mutex.hpp:98  
#2  boost::asio::detail::posix_event::wait<boost::asio::detail::conditionally_enabled_mutex::scoped_lock> (lock=..., this=0x162e310) at /usr/local/include/boost/asio/detail/posix_event.hpp:113  
#3  boost::asio::detail::conditionally_enabled_event::wait (lock=..., this=0x162e308) at /usr/local/include/boost/asio/detail/conditionally_enabled_event.hpp:97  
#4  boost::asio::detail::scheduler::do_run_one (ec=..., this_thread=..., lock=..., this=0x162e2a0) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:501  
#5  boost::asio::detail::scheduler::run (this=0x162e2a0, ec=...) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:210  
#6  0x00007f26f94bf9d9 in boost::asio::system_context::thread_function::operator() (this=<optimized out>) at /usr/local/include/boost/asio/impl/system_context.ipp:37  
#7  boost::asio::detail::posix_thread::func<boost::asio::system_context::thread_function>::run (this=<optimized out>) at /usr/local/include/boost/asio/detail/posix_thread.hpp:86  
#8  0x0000000000435510 in boost::asio::detail::boost_asio_detail_posix_thread_function (arg=0x15a8650) at /usr/local/include/boost/asio/detail/impl/posix_thread.ipp:74  
#9  0x00007f26fa1d717a in start_thread () from /lib64/libpthread.so.0  
#10 0x00007f26f852ddc3 in clone () from /lib64/libc.so.6  
Thread 2 (Thread 0x7f26ec154700 (LWP 1010744) "td_proxy"):  
#0  0x00007f26f852e0f7 in epoll_wait () from /lib64/libc.so.6  
#1  0x0000000000437a36 in boost::asio::detail::epoll_reactor::run (this=0x159db30, usec=<optimized out>, ops=...) at /usr/local/include/boost/asio/detail/impl/epoll_reactor.ipp:501  
#2  0x000000000043a64b in boost::asio::detail::scheduler::do_run_one (ec=..., this_thread=..., lock=..., this=0x159d3a0) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:476  
#3  boost::asio::detail::scheduler::run (this=0x159d3a0, ec=...) at /usr/local/include/boost/asio/detail/impl/scheduler.ipp:210  
#4  0x00000000004308e6 in boost::asio::io_context::run (this=<optimized out>, this=<optimized out>) at /usr/local/include/boost/asio/impl/io_context.ipp:63  
#5  operator() (__closure=<optimized out>) at /home/FastTrader/td_proxy/src/trader_engine.cc:93  
#6  std::__invoke_impl<void, fast_trader::td::trader_engine::start_net()::<lambda()> > (__f=...) at /opt/rh/gcc-toolset-11/root/usr/include/c++/11/bits/invoke.h:61  
#7  std::__invoke<fast_trader::td::trader_engine::start_net()::<lambda()> > (__fn=...) at /opt/rh/gcc-toolset-11/root/usr/include/c++/11/bits/invoke.h:96  
#8  std::thread::_Invoker<std::tuple<fast_trader::td::trader_engine::start_net()::<lambda()> > >::_M_invoke<0> (this=<optimized out>) at /opt/rh/gcc-toolset-11/root/usr/include/c++/11/bits/std_thread.h:253  
#9  std::thread::_Invoker<std::tuple<fast_trader::td::trader_engine::start_net()::<lambda()> > >::operator() (this=<optimized out>) at /opt/rh/gcc-toolset-11/root/usr/include/c++/11/bits/std_thread.h:260  
#10 std::thread::_State_impl<std::thread::_Invoker<std::tuple<fast_trader::td::trader_engine::start_net()::<lambda()> > > >::_M_run(void) (this=<optimized out>) at /opt/rh/gcc-toolset-11/root/usr/include/c++/11/bits/std_thread.h:211  
#11 0x00007f26f8e52ba3 in execute_native_thread_routine () from /lib64/libstdc++.so.6  
#12 0x00007f26fa1d717a in start_thread () from /lib64/libpthread.so.0  
#13 0x00007f26f852ddc3 in clone () from /lib64/libc.so.6  
Thread 1 (Thread 0x7f26fa5e8380 (LWP 1010743) "td_proxy"):  
#0  0x00007f26fa1d866d in __pthread_timedjoin_ex () from /lib64/libpthread.so.0  
#1  0x00007f26f8e52e27 in std::thread::join() () from /lib64/libstdc++.so.6  
#2  0x00000000004201dd in main () at /home/FastTrader/td_proxy/src/main.cc:26  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-06-03 18:32:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2874#issuecomment-2145864994  

In these cases I find stack dumps to be less helpful. Instead, I prefer to have a detailed explanation of how the program manages the lifetime of its connections. For example, this is how I would explain it for the example async servers in beast:  
  
1. The completion handler for every asynchronous operation acquires shared ownership of the connection object by retaining a copy of its shared pointer  
2. The lifetime of the connection object is extended when a completion handler initiates another asynchronous operation for the same connection before returning  
3. The connection object is destroyed when the last outstanding completion handler exits  
  
and   
  
4. completion handlers for asynchronous operations on connections associated with multi-threaded I/O contexts is posted through a strand  
  
What are the rules for your application?

---

## Comment 5

> Username: ahao1995  
> Created at: 2024-06-04 00:34:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2874#issuecomment-2146351645  

> In these cases I find stack dumps to be less helpful. Instead, I prefer to have a detailed explanation of how the program manages the lifetime of its connections. For example, this is how I would explain it for the example async servers in beast:  
>   
> 1. The completion handler for every asynchronous operation acquires shared ownership of the connection object by retaining a copy of its shared pointer  
> 2. The lifetime of the connection object is extended when a completion handler initiates another asynchronous operation for the same connection before returning  
> 3. The connection object is destroyed when the last outstanding completion handler exits  
>   
> and  
>   
> 4. completion handlers for asynchronous operations on connections associated with multi-threaded I/O contexts is posted through a strand  
>   
> What are the rules for your application?  
  
maybe it is life time issue, I use raw pointer and delete it when it close

---

## Comment 6

> Username: vinniefalco  
> Created at: 2024-06-04 15:06:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2874#issuecomment-2147773553  

> I use raw pointer and delete it when it close  
  
What happens when there are concurrent asynchronous operations for the same connection? Instead of using a raw pointer, try using `std::unique_ptr<connection>`. This will guarantee that you do not have more than one operation active at the same time, as any attempt to launch a second operation while one is pending will immediately throw.

---

## Comment 7

> Username: ahao1995  
> Created at: 2024-06-06 01:09:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2874#issuecomment-2151211804  

> > I use raw pointer and delete it when it close  
>   
> What happens when there are concurrent asynchronous operations for the same connection? Instead of using a raw pointer, try using `std::unique_ptr<connection>`. This will guarantee that you do not have more than one operation active at the same time, as any attempt to launch a second operation while one is pending will immediately throw.  
  
thanks
