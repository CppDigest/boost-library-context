# #1931 - Assertion after SIGINT signal while async_read operations are active [Closed]

> Username: Ban44n  
> Created at: 2020-05-01 13:35:25 UTC  
> Updated at: 2020-05-05 00:18:44 UTC  
> Closed at: 2020-05-05 00:18:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1931  

We experience a coredump error when abrupt closing beast using a SIGINT signal while there are still async read operations pending to finish. This signal leads to a cleanup in our code, but some shared_ptrs are still alive because beast could be in the middle of a read or write operation during the SIGINT. We have a timeout policy enabled on the link that kills it after 1 second.  
  
The core dump that occurs:  
```  
 /usr/local/include/boost/beast/websocket/impl/read.hpp:112: void boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::read_some_op< <template-parameter-2-1>, <template-parameter-2-2> >::operator()(boost::beast::error_code, std::size_t, bool) [with Handler = boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> > >::read_op<App::Transport::Session::Base::on_read_loop(boost::beast::error_code, std::size_t, const read_callback&)::<lambda(boost::beast::error_code, std::size_t)>, boost::beast::basic_flat_buffer<std::allocator<char> > >; MutableBufferSequence = boost::asio::mutable_buffer; NextLayer = boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> >; bool deflateSupported = true; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `! ec && impl.check_stop_now(ec)' failed.  
Aborted (core dumped)  
```  
  
A path in read.hpp:112 leads to a coredump in the beast library after a timeout triggers in the read operation. The stack trace is:  
  
```  
libc.so.6!__GI_raise(int sig) (/build/glibc-t7JzpG/glibc-2.30/sysdeps/unix/sysv/linux/raise.c:50)  
libc.so.6!__GI_abort() (/build/glibc-t7JzpG/glibc-2.30/stdlib/abort.c:79)  
libc.so.6!__assert_fail_base(const char * fmt, const char * assertion, const char * file, unsigned int line, const char * function) (/build/glibc-t7JzpG/glibc-2.30/assert/assert.c:92)  
libc.so.6!__GI___assert_fail(const char * assertion, const char * file, unsigned int line, const char * function) (/build/glibc-t7JzpG/glibc-2.30/assert/assert.c:101)  
ws_node.node!boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> >, true>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> > >::read_op<App::Transport::Session::Base::on_read_loop(boost::beast::error_code, std::size_t, const read_callback&)::<lambda(boost::beast::error_code, std::size_t)>, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>::operator()(boost::beast::error_code, std::size_t, bool)(boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> >, true>::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> > >::read_op<App::Transport::Session::Base::on_read_loop(boost::beast::error_code, std::size_t, const read_callback&)::<lambda(boost::beast::error_code, std::size_t)>, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer> * const this, boost::beast::error_code ec, std::size_t bytes_transferred, bool cont) (/usr/local/include/boost/beast/websocket/impl/read.hpp:112)  
ws_node.node!boost::asio::detail::executor_function<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> > >::read_some_op<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::simple_rate_policy> > >::read_op<App::Transport::Session::Base::on_read_loop(boost::beast::error_code, std::size_t, const read_callback&)::<lambda(boost::beast::error_code, std::size_t)>, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>, std::allocator<void> >::do_complete(boost::asio::detail::executor_function_base *, bool)(boost::asio::detail::executor_function_base * base, bool call) (/usr/local/include/boost/asio/detail/executor_function.hpp:91)  
ws_node.node!boost::asio::detail::executor_function_base::complete(boost::asio::detail::executor_function_base * const this) (/usr/local/include/boost/asio/detail/executor_function.hpp:32)  
ws_node.node!boost::asio::executor::function::operator()(boost::asio::executor::function * const this) (/usr/local/include/boost/asio/impl/executor.hpp:69)  
ws_node.node!boost::asio::asio_handler_invoke<boost::asio::executor::function>(boost::asio::executor::function & function) (/usr/local/include/boost/asio/handler_invoke_hook.hpp:69)  
ws_node.node!boost_asio_handler_invoke_helpers::invoke<boost::asio::executor::function, boost::asio::executor::function>(boost::asio::executor::function & function, boost::asio::executor::function & context) (/usr/local/include/boost/asio/detail/handler_invoke_helpers.hpp:37)  
ws_node.node!boost::asio::detail::handler_work<boost::asio::executor::function, boost::asio::system_executor, boost::asio::system_executor>::complete<boost::asio::executor::function>(boost::asio::detail::handler_work<boost::asio::executor::function, boost::asio::system_executor, boost::asio::system_executor> * const this, boost::asio::executor::function & function, boost::asio::executor::function & handler) (/usr/local/include/boost/asio/detail/handler_work.hpp:100)  
ws_node.node!boost::asio::detail::completion_handler<boost::asio::executor::function>::do_complete(void * owner, boost::asio::detail::operation * base) (/usr/local/include/boost/asio/detail/completion_handler.hpp:70)  
ws_node.node!boost::asio::detail::scheduler_operation::complete(boost::asio::detail::scheduler_operation * const this, void * owner, const boost::system::error_code & ec, std::size_t bytes_transferred) (/usr/local/include/boost/asio/detail/scheduler_operation.hpp:40)  
ws_node.node!boost::asio::detail::strand_service::do_complete(void * owner, boost::asio::detail::operation * base, const boost::system::error_code & ec) (/usr/local/include/boost/asio/detail/impl/strand_service.ipp:168)  
ws_node.node!boost::asio::detail::scheduler_operation::complete(boost::asio::detail::scheduler_operation * const this, void * owner, const boost::system::error_code & ec, std::size_t bytes_transferred) (/usr/local/include/boost/asio/detail/scheduler_operation.hpp:40)  
ws_node.node!boost::asio::detail::scheduler::do_run_one(boost::asio::detail::scheduler * const this, boost::asio::detail::conditionally_enabled_mutex::scoped_lock & lock, boost::asio::detail::scheduler::thread_info & this_thread, const boost::system::error_code & ec) (/usr/local/include/boost/asio/detail/impl/scheduler.ipp:447)  
ws_node.node!boost::asio::detail::scheduler::run(boost::asio::detail::scheduler * const this, boost::system::error_code & ec) (/usr/local/include/boost/asio/detail/impl/scheduler.ipp:200)  
ws_node.node!boost::asio::io_context::run(boost::asio::io_context * const this) (/usr/local/include/boost/asio/impl/io_context.ipp:63)  
ws_node.node!App::Pool::Context::<lambda()>::operator()(void) const(const App::Pool::Context::<lambda()> * const __closure) (/home/x/projects/ws/src/pool/context.cpp:48)  
ws_node.node!std::__invoke_impl<void, App::Pool::Context::start()::<lambda()> >(std::__invoke_other, App::Pool::Context::<lambda()> &&)(App::Pool::Context::<lambda()> && __f) (/usr/include/c++/9/bits/invoke.h:60)  
ws_node.node!std::__invoke<App::Pool::Context::start()::<lambda()> >(App::Pool::Context::<lambda()> &&)(App::Pool::Context::<lambda()> && __fn) (/usr/include/c++/9/bits/invoke.h:95)  
ws_node.node!std::thread::_Invoker<std::tuple<App::Pool::Context::start()::<lambda()> > >::_M_invoke<0>(std::_Index_tuple<0>)(std::thread::_Invoker<std::tuple<App::Pool::Context::start()::<lambda()> > > * const this) (/usr/include/c++/9/thread:244)  
ws_node.node!std::thread::_Invoker<std::tuple<App::Pool::Context::start()::<lambda()> > >::operator()(void)(std::thread::_Invoker<std::tuple<App::Pool::Context::start()::<lambda()> > > * const this) (/usr/include/c++/9/thread:251)  
ws_node.node!std::thread::_State_impl<std::thread::_Invoker<std::tuple<App::Pool::Context::start()::<lambda()> > > >::_M_run(void)(std::thread::_State_impl<std::thread::_Invoker<std::tuple<App::Pool::Context::start()::<lambda()> > > > * const this) (/usr/include/c++/9/thread:195)  
libstdc++.so.6![Unknown/Just-In-Time compiled code] (Unknown Source:0)  
libpthread.so.0!start_thread(void * arg) (/build/glibc-t7JzpG/glibc-2.30/nptl/pthread_create.c:479)  
libc.so.6!clone() (/build/glibc-t7JzpG/glibc-2.30/sysdeps/unix/sysv/linux/x86_64/clone.S:95)  
```  
  
Our code is not touched in the stack trace, except in our Pool class that manages a asio io_context pool that is still alive during the crash.  
  
We expect the following error occurs:  
  
In read.hpp:112 we have:  
```  
BOOST_ASSERT(! ec && impl.check_stop_now(ec));  
```  
We see from inspecting the coredump file that ec._value = 0 (i.e. no error yet) during the crash, so the first statement in the ASSERT is true. In order for this ASSERT to trigger, we must have that check_stop_now(ec) returns false. We open the file that contains the check_stop_now() function  
```  
include/boost/beast/websocket/impl/stream_impl.hpp:331  
```  
  
We know from our coredump file that status_ = status::closing during the crash, such that the only possible path that could lead to this ASSERTION is the return false statement. As a result the ASSERT is triggered.  
  
I think the possible way to fix this is just to remove the BOOST_ASSERT all together or take this path into account.  
  
### Version of Beast  
The version is 248, but we see the same bug in the develop branch.  
  
### Steps necessary to reproduce the problem  
  
Fire a lot of traffic and a lot of read operations, enable a timeout policy, do not explicitly close the link during a SIGNINT signal.  
  
### All relevant compiler information  
g++ (Ubuntu 9.2.1-9ubuntu2) 9.2.1 20191008

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-01 14:11:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1931#issuecomment-622403811  

I'd be interested to know more. Are you able to join me on slack?  
in the #beast channel?  
https://cppalliance.org/slack/

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-05-01 14:55:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1931#issuecomment-622420918  

Many thanks filing an excellent bug report and for agreeing to join us on slack to help diagnose it.  
  
I'll make a patch available very shortly.
