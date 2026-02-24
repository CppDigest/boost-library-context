# #84 Added example cpp20_streams, which reproduces an assertion. [Closed]

> Username: bveldhoen  
> Created at: 2023-03-15 21:27:40 UTC  
> Updated at: 2023-05-06 19:29:34 UTC  
> Closed at: 2023-05-06 19:29:34 UTC  
> Url: https://github.com/boostorg/redis/pull/84  

This PR adds an example for using redis streams:  
https://redis.io/docs/data-types/streams/  
  
Points of attention:  
  
* The redis::connection is constructed by providing strand, created with make_strand, as the constructor argument.  
* Calls to async_exec are explicitly performed on the executor of the redis::connection, which is that strand. Therefore, there shouldn't be any concurrent executions of async_exec or on_exec (the callback handler). Please verify if this is correct?  
* cpp20_streams example is built with option -g.  
  
I'm running into an assertion, which causes the process to abort:  
  
```  
cpp20_streams: /home/user/src/projects/redis/include/boost/redis/detail/connection_ops.hpp:382: ... Assertion `conn->is_waiting_response()' failed.  
```  
  
Steps to reproduce:  
  
```  
$ mkdir -p build && cd build  
$ BOOST_ROOT=/home/user/src/projects/boost cmake ..  
$ make -j4  
(terminal1) $ ./cpp20_streams  
(terminal2) $ redis-cli -r 100000 -i 0.0001 XADD "test-topic" "*" "myfield" "myfieldvalue1"  
```  
  
Below the relevant part of the gdb output:  
  
```  
...  
Thread 1 (Thread 0x7f1f851fe640 (LWP 18378)):  
#0  __pthread_kill_implementation (no_tid=0, signo=6, threadid=139773354174016) at ./nptl/pthread_kill.c:44  
#1  __pthread_kill_internal (signo=6, threadid=139773354174016) at ./nptl/pthread_kill.c:78  
#2  __GI___pthread_kill (threadid=139773354174016, signo=signo@entry=6) at ./nptl/pthread_kill.c:89  
#3  0x00007f1f85a42476 in __GI_raise (sig=sig@entry=6) at ../sysdeps/posix/raise.c:26  
#4  0x00007f1f85a287f3 in __GI_abort () at ./stdlib/abort.c:79  
#5  0x00007f1f85a2871b in __assert_fail_base (fmt=0x7f1f85bdd150 "%s%s%s:%u: %s%sAssertion `%s' failed.\n%n", assertion=0x55d244071dc3 "conn->is_waiting_response()", file=0x55d244070c60 "/home/user/src/projects/boost/boost/redis/detail/connection_ops.hpp", line=382, function=<optimized out>) at ./assert/assert.c:92  
#6  0x00007f1f85a39e96 in __GI___assert_fail (assertion=0x55d244071dc3 "conn->is_waiting_response()", file=0x55d244070c60 "/home/user/src/projects/boost/boost/redis/detail/connection_ops.hpp", line=382, function=0x55d244071fb0 "void boost::redis::detail::reader_op<Conn>::operator()(Self&, boost::system::error_code, std::size_t) [with Self = boost::asio::detail::composed_op<boost::redis::detail::reader_op<boost::redis::basic_"...) at ./assert/assert.c:101  
#7  0x000055d24404af25 in boost::redis::detail::reader_op<boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::operator()...::{lambda(auto:1)#1}, void (boost::system::error_code)>&, boost::system::error_code, unsigned long) (this=0x7f1f851fd148, self=..., ec=..., n=4) at /home/user/src/projects/boost/boost/redis/detail/connection_ops.hpp:382  
...  
#46 0x000055d2440117ad in boost::asio::io_context::run (this=0x7ffc730ae590) at /home/user/src/projects/boost/boost/asio/impl/io_context.ipp:63  
#47 0x000055d2440074d3 in operator() (__closure=0x55d244a5db28) at /home/user/src/projects/redis/examples/cpp20_streams.cpp:234  
#48 0x000055d24400837e in std::__invoke_impl<void, main(int, char**)::<lambda()> >(std::__invoke_other, struct {...} &&) (__f=...) at /usr/include/c++/11/bits/invoke.h:61  
#49 0x000055d244008341 in std::__invoke<main(int, char**)::<lambda()> >(struct {...} &&) (__fn=...) at /usr/include/c++/11/bits/invoke.h:96  
#50 0x000055d2440082ee in std::thread::_Invoker<std::tuple<main(int, char**)::<lambda()> > >::_M_invoke<0>(std::_Index_tuple<0>) (this=0x55d244a5db28) at /usr/include/c++/11/bits/std_thread.h:253  
#51 0x000055d2440082c2 in std::thread::_Invoker<std::tuple<main(int, char**)::<lambda()> > >::operator()(void) (this=0x55d244a5db28) at /usr/include/c++/11/bits/std_thread.h:260  
#52 0x000055d2440082a6 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<main(int, char**)::<lambda()> > > >::_M_run(void) (this=0x55d244a5db20) at /usr/include/c++/11/bits/std_thread.h:211  
#53 0x00007f1f85edc2b3 in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#54 0x00007f1f85a94b43 in start_thread (arg=<optimized out>) at ./nptl/pthread_create.c:442  
#55 0x00007f1f85b26a00 in clone3 () at ../sysdeps/unix/sysv/linux/x86_64/clone3.S:81  
(gdb) frame 7  
#7  0x000055d24404af25 in boost::redis::detail::reader_op<boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::operator()...::{lambda(auto:1)#1}, void (boost::system::error_code)>&, boost::system::error_code, unsigned long) (this=0x7f1f851fd148, self=..., ec=..., n=4) at /home/user/src/projects/boost/boost/redis/detail/connection_ops.hpp:382  
382	            BOOST_ASSERT(conn->is_waiting_response());  
(gdb) info frame  
Stack level 7, frame at 0x7f1f851fcca0:  
 rip = 0x55d24404af25 in boost::redis::detail::reader_op<boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::operator()...::{lambda(auto:1)#1}, void (boost::system::error_code)>&, boost::system::error_code, unsigned long) (/home/user/src/projects/boost/boost/redis/detail/connection_ops.hpp:382); saved rip = 0x55d24405631d  
 called by frame at 0x7f1f851fcd10, caller of frame at 0x7f1f851fcbb0  
 source language c++.  
 Arglist at 0x7f1f851fcc90, args: this=0x7f1f851fd148, self=..., ec=..., n=4  
 Locals at 0x7f1f851fcc90, Previous frame's sp is 0x7f1f851fcca0  
 Saved registers:  
  rbx at 0x7f1f851fcc88, rbp at 0x7f1f851fcc90, rip at 0x7f1f851fcc98  
(gdb) p conn  
$1 = (boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > *) 0x55d244a5c4a8  
(gdb) p conn->reqs_  
$2 = std::deque with 1 element = {warning: RTTI symbol not found for class 'std::_Sp_counted_ptr_inplace<boost::redis::detail::connection_base<boost::asio::any_io_executor, boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::req_info, std::allocator<void>, (__gnu_cxx::_Lock_policy)2>'  
warning: RTTI symbol not found for class 'std::_Sp_counted_ptr_inplace<boost::redis::detail::connection_base<boost::asio::any_io_executor, boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::req_info, std::allocator<void>, (__gnu_cxx::_Lock_policy)2>'  
std::shared_ptr<boost::redis::detail::connection_base<boost::asio::any_io_executor, boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::req_info> (use count 2, weak count 0) = {get() = 0x7f1f6c004cf0}}  
(gdb) p conn->reqs_.front()->status_  
$3 = boost::redis::detail::connection_base<boost::asio::any_io_executor, boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::req_info::status::staged  
(gdb) p conn->reqs_.size()  
$4 = 24  
(gdb) p conn->reqs_.front()  
$5 = warning: RTTI symbol not found for class 'std::_Sp_counted_ptr_inplace<boost::redis::detail::connection_base<boost::asio::any_io_executor, boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::req_info, std::allocator<void>, (__gnu_cxx::_Lock_policy)2>'  
warning: RTTI symbol not found for class 'std::_Sp_counted_ptr_inplace<boost::redis::detail::connection_base<boost::asio::any_io_executor, boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::req_info, std::allocator<void>, (__gnu_cxx::_Lock_policy)2>'  
std::shared_ptr<boost::redis::detail::connection_base<boost::asio::any_io_executor, boost::redis::basic_connection<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::req_info> (use count 2, weak count 0) = {get() = 0x7f1f6c004cf0}  
(gdb) p conn->reqs_.front()->req_  
$6 = (const boost::redis::request *) 0x55d244a5c770  
(gdb) p *conn->reqs_.front()->req_  
$7 = {cfg_ = {cancel_on_connection_lost = true, cancel_if_not_connected = false, cancel_if_unresponded = true, hello_with_priority = true}, payload_ = "*6\r\n$5\r\nXREAD\r\n$5\r\nBLOCK\r\n$1\r\n0\r\n$7\r\nSTREAMS\r\n$10\r\ntest-topic\r\n$15\r\n1678910640399-2\r\n", commands_ = 1, has_hello_priority_ = false}  
  
```  
  
A relevant detail is that conn->reqs_.front()->status_ has a value of _staged_ (see $3 above), where the assertion expects it to have value _written_ (?)

---

## Comment 1

> Username: mzimbres  
> Created_at: 2023-03-16 09:06:59 UTC  
> Url: https://github.com/boostorg/redis/pull/84#issuecomment-1471563526  

Thanks. I am working on another PR at the moment and will be on vacation soon, so it might take some time before I can merge this.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2023-05-06 19:29:34 UTC  
> Url: https://github.com/boostorg/redis/pull/84#issuecomment-1537208054  

I ended up opening a new PR where I kept your commits and made the necessary adjustments to rebase on top of develop. There was a large merge in the develop with lots of high-level features, which made the implementation easier. You might want to incorporate that in your branch.

---
