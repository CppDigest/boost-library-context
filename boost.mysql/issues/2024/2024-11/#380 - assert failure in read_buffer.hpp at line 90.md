# #380 - assert failure in read_buffer.hpp at line 90 [Closed]

> Username: RavikumarTulugu  
> Created at: 2024-11-11 05:31:59 UTC  
> Updated at: 2024-11-12 09:30:15 UTC  
> Closed at: 2024-11-11 15:19:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/380  

I am using boost 1.86 and seeing a random assert failure at times.  the same query works fine but asserts at times. wondering what is the cause for below assert failure,   
  
**/work/opensource//base/boost_1_86_0/boost/mysql/impl/internal/sansio/read_buffer.hpp:90: void boost::mysql::detail::read_buffer::move_to_pending(std::size_t): Assertion `length <= free_size()' failed.**  
  
.  
```  
__pthread_kill_implementation (no_tid=0, signo=6, threadid=140380644833856) at ./nptl/pthread_kill.c:44  
44      ./nptl/pthread_kill.c: No such file or directory.  
(gdb) bt  
#0  __pthread_kill_implementation (no_tid=0, signo=6, threadid=140380644833856) at ./nptl/pthread_kill.c:44  
#1  __pthread_kill_internal (signo=6, threadid=140380644833856) at ./nptl/pthread_kill.c:78  
#2  __GI___pthread_kill (threadid=140380644833856, signo=signo@entry=6) at ./nptl/pthread_kill.c:89  
#3  0x00007facea8a1476 in __GI_raise (sig=sig@entry=6) at ../sysdeps/posix/raise.c:26  
#4  0x00007facea8877f3 in __GI_abort () at ./stdlib/abort.c:79  
#5  0x00007facea88771b in __assert_fail_base (fmt=0x7faceaa3c130 "%s%s%s:%u: %s%sAssertion `%s' failed.\n%n", assertion=0x55bc6231097e "length <= free_size()",   
    file=0x55bc623108df "/work/opensource//base/boost_1_86_0/boost/mysql/impl/internal/sansio/read_buffer.hpp", line=90, function=<optimized out>) at ./assert/assert.c:92  
#6  0x00007facea898e96 in __GI___assert_fail (assertion=0x55bc6231097e "length <= free_size()", file=0x55bc623108df "/work/opensource//base/boost_1_86_0/boost/mysql/impl/internal/sansio/read_buffer.hpp",   
    line=90, function=0x55bc62310994 "void boost::mysql::detail::read_buffer::move_to_pending(std::size_t)") at ./assert/assert.c:101  
#7  0x000055bc6208a942 in boost::mysql::detail::read_buffer::move_to_pending (this=0x55bc6364fe38, length=1024) at opensource/base/boost_1_86_0/boost/mysql/impl/internal/sansio/read_buffer.hpp:90  
#8  0x000055bc6208a429 in boost::mysql::detail::message_reader::resume (this=0x55bc6364fe38, bytes_read=1024) at opensource/base/boost_1_86_0/boost/mysql/impl/internal/sansio/message_reader.hpp:109  
#9  0x000055bc62098a11 in boost::mysql::detail::top_level_algo<boost::mysql::detail::prepare_statement_algo>::resume (this=0x55bc6364fec0, ec=..., bytes_transferred=1024)  
    at opensource/base/boost_1_86_0/boost/mysql/impl/internal/sansio/top_level_algo.hpp:96  
#10 0x000055bc6209873c in boost::mysql::detail::any_resumable_ref::do_resume<boost::mysql::detail::top_level_algo<boost::mysql::detail::prepare_statement_algo> > (self=0x55bc6364fec0, ec=...,   
    bytes_transferred=1024) at opensource/base/boost_1_86_0/boost/mysql/detail/any_resumable_ref.hpp:25  
#11 0x000055bc62200610 in boost::mysql::detail::any_resumable_ref::resume (this=0x7ffe3dd21178, ec=..., bytes_transferred=1024) at opensource/base/boost_1_86_0/boost/mysql/detail/any_resumable_ref.hpp:41  
#12 0x000055bc622001af in boost::mysql::detail::run_algo_op<boost::mysql::detail::engine_stream_adaptor<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >::operator()<boost::asio::detail::composed_op<boost::mysql::detail::run_algo_op<boost::mysql::detail::engine_stream_adaptor<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::any_completion_handler<void (boost::system::error_code)>, void (boost::system::error_code)> >(boost::asio::detail::composed_op<boost::mysql::detail::run_algo_op<boost::mysql::detail::engine_stream_adaptor<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::any_completion_handler<void (boost::system::error_code)>, void (boost::system::error_code)>&, boost::system::error_code, unsigned long) (this=0x7ffe3dd21168, self=...,   
    io_ec=..., bytes_transferred=1024) at opensource/base/boost_1_86_0/boost/mysql/detail/engine_impl.hpp:60  
#13 0x000055bc62207c4a in boost::asio::detail::composed_op<boost::mysql::detail::run_algo_op<boost::mysql::detail::engine_stream_adaptor<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::any_completion_handler<void (boost::system::error_code)>, void (boost::system::error_code)>::operator()<boost::system::error_code const&, unsigned long const&>(boost::system::error_code const&, unsigned long const&) (this=0x7ffe3dd21160, t=@0x7ffe3dd21210: 1024, t=@0x7ffe3dd21210: 1024)  
    at opensource/base/boost_1_86_0/boost/asio/composed.hpp:149  
#14 0x000055bc62207b99 in boost::asio::detail::binder2<boost::asio::detail::composed_op<boost::mysql::detail::run_algo_op<boost::mysql::detail::engine_stream_adaptor<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::any_completion_handler<void (boost::system::error_code)>, void (boost::system::error_code)>, boost::system::error_code, unsigned long>::operator()() (this=0x7ffe3dd21160) at opensource/base/boost_1_86_0/boost/asio/detail/bind_handler.hpp:181  
#15 0x000055bc62207b65 in boost::asio::detail::binder0<boost::asio::detail::binder2<boost::asio::detail::composed_op<boost::mysql::detail::run_algo_op<boost::mysql::detail::engine_stream_adaptor<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::any_completion_handler<void (boost::system::error_code)>, void (boost::system::error_code)>, boost::system::error_code, unsigned long> >::operator()() (this=0x7ffe3dd21160) at opensource/base/boost_1_86_0/boost/asio/detail/bind_handler.hpp:56  
#16 0x000055bc62207ada in boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::asio::detail::binder2<boost::asio::detail::composed_op<boost::mysql::detail::run_algo_op<boost::mysql::detail::engine_stream_adaptor<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::any_io_executor> > >, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::asio::any_completion_handler<void (boost::system::error_code)>, void (boost::system::error_code)>, boost::system::error_code, unsigned long> >, std::allocator<void> >(boost::asio::detail::executor_function::impl_base*, bool) (base=0x55bc636afdd0, call=true) at opensource/base/boost_1_86_0/boost/asio/detail/executor_function.hpp:113  
#17 0x000055bc620537ae in boost::asio::detail::executor_function::operator() (this=0x7ffe3dd212b8) at opensource/base/boost_1_86_0/boost/asio/detail/executor_function.hpp:61  
#18 0x000055bc62054229 in agrpc::b::detail::NoArgOperation<boost::asio::detail::executor_function>::do_complete<false> (op=0x55bc6369c3a0, result=agrpc::b::detail::OperationResult::OK_)  
    at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/operation.hpp:55  
#19 0x000055bc62026203 in agrpc::b::detail::OperationBase::complete (this=0x55bc6369c3a0, result=agrpc::b::detail::OperationResult::OK_, grpc_context=...)  
    at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/operation_base.hpp:46  
#20 0x000055bc62025b41 in agrpc::b::detail::GrpcContextImplementation::process_local_queue (context=..., invoke=agrpc::b::detail::InvokeHandler::YES_)  
    at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_implementation_definition.hpp:166  
#21 0x000055bc62032946 in agrpc::b::detail::GrpcContextImplementation::do_one<false> (context=..., deadline=..., invoke=agrpc::b::detail::InvokeHandler::YES_)  
    at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_implementation_definition.hpp:226  
#22 0x000055bc620328c6 in agrpc::b::detail::GrpcContextImplementation::do_one_if_not_stopped<false> (context=..., deadline=...)  
    at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_implementation_definition.hpp:252  
#23 0x000055bc620327c7 in agrpc::b::GrpcContext::poll()::{lambda(auto:1&)#1}::operator()<agrpc::b::detail::GrpcContextThreadContextImpl<false> >(agrpc::b::detail::GrpcContextThreadContextImpl<false>&) const (  
--Type <RET> for more, q to quit, c to continue without paging--  
    this=0x7ffe3dd21690, context=...) at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_definition.hpp:154  
#24 0x000055bc62032715 in agrpc::b::detail::GrpcContextImplementation::process_work<agrpc::b::detail::GrpcContextLoopFunction<agrpc::b::GrpcContext::poll()::{lambda(auto:1&)#1}> >(agrpc::b::GrpcContext&, agrpc::b::detail::GrpcContextLoopFunction<agrpc::b::GrpcContext::poll()::{lambda(auto:1&)#1}>)::{lambda(auto:1&)#1}::operator()<agrpc::b::detail::GrpcContextThreadContextImpl<false> >(agrpc::b::detail::GrpcContextThreadContextImpl<false>&) const (this=0x7ffe3dd21680, thread_context=...) at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_implementation_definition.hpp:280  
#25 0x000055bc62032687 in agrpc::b::detail::GrpcContextImplementation::process_work<agrpc::b::detail::GrpcContextLoopFunction<agrpc::b::GrpcContext::poll()::{lambda(auto:1&)#1}> >(agrpc::b::GrpcContext&, agrpc::b::detail::GrpcContextLoopFunction<agrpc::b::GrpcContext::poll()::{lambda(auto:1&)#1}>)::{lambda(auto:1)#2}::operator()<std::integral_constant<bool, false> >(std::integral_constant<bool, false>) const (  
    this=0x7ffe3dd21628, v=...) at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_implementation_definition.hpp:307  
#26 0x000055bc62032587 in _ZN5agrpc1b6detail25GrpcContextImplementation22visit_is_multithreadedIZNS2_12process_workINS1_23GrpcContextLoopFunctionIZNS0_11GrpcContext4pollEvEUlRT_E_EEEEbRS6_S7_EUlS7_E0_EEDcRKS6_S7_ (grpc_context=..., function=...) at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_implementation_definition.hpp:351  
  
#27 0x000055bc620324b8 in agrpc::b::detail::GrpcContextImplementation::process_work<agrpc::b::detail::GrpcContextLoopFunction<agrpc::b::GrpcContext::poll()::{lambda(auto:1&)#1}> >(agrpc::b::GrpcContext&, agrpc::b::detail::GrpcContextLoopFunction<agrpc::b::GrpcContext::poll()::{lambda(auto:1&)#1}>) (grpc_context=..., loop_function=...)  
    at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_implementation_definition.hpp:302  
#28 0x000055bc620323a5 in agrpc::b::GrpcContext::poll (this=0x7ffe3dd227d0) at opensource/base/asio-grpc-3.2.0/src/agrpc/detail/grpc_context_definition.hpp:151  
#29 0x000055bc62020adc in grpcPollTimerCallback (error=..., timer=0x7ffe3dd222a8, ioc=..., grpc_context=...) at dbsvr.cpp:1341  
#30 0x000055bc620319f8 in boost::_bi::list<boost::arg<1>, boost::_bi::value<boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*>, boost::_bi::value<std::reference_wrapper<boost::asio::io_context> >, boost::_bi::value<std::reference_wrapper<agrpc::b::GrpcContext> > >::call_impl<void (*)(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&), boost::_bi::rrlist<boost::system::error_code const&>, 0ul, 1ul, 2ul, 3ul> (this=0x7ffe3dd21988,   
    f=@0x7ffe3dd21980: 0x55bc62020880 <grpcPollTimerCallback(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&)>, a2=...) at opensource/base/boost_1_86_0/boost/bind/bind.hpp:192  
#31 0x000055bc62031915 in boost::_bi::list<boost::arg<1>, boost::_bi::value<boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*>, boost::_bi::value<std::reference_wrapper<boost::asio::io_context> >, boost::_bi::value<std::reference_wrapper<agrpc::b::GrpcContext> > >::operator()<void, void (*)(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&), boost::_bi::rrlist<boost::system::error_code const&> > (this=0x7ffe3dd21988,   
    f=@0x7ffe3dd21980: 0x55bc62020880 <grpcPollTimerCallback(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&)>, a2=...) at opensource/base/boost_1_86_0/boost/bind/bind.hpp:208  
#32 0x000055bc620318b9 in boost::_bi::bind_t<void, void (*)(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&), boost::_bi::list<boost::arg<1>, boost::_bi::value<boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*>, boost::_bi::value<std::reference_wrapper<boost::asio::io_context> >, boost::_bi::value<std::reference_wrapper<agrpc::b::GrpcContext> > > >::operator()<boost::system::error_code const&> (this=0x7ffe3dd21980, a=...) at opensource/base/boost_1_86_0/boost/bind/bind.hpp:321  
#33 0x000055bc620310bc in boost::asio::detail::binder1<boost::_bi::bind_t<void, void (*)(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&), boost::_bi::list<boost::arg<1>, boost::_bi::value<boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*>, boost::_bi::value<std::reference_wrapper<boost::asio::io_context> >, boost::_bi::value<std::reference_wrapper<agrpc::b::GrpcContext> > > >, boost::system::error_code>::operator() (this=0x7ffe3dd21980) at opensource/base/boost_1_86_0/boost/asio/detail/bind_handler.hpp:115  
#34 0x000055bc62030fee in boost::asio::detail::handler_work<boost::_bi::bind_t<void, void (*)(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&), boost::_bi::list<boost::arg<1>, boost::_bi::value<boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*>, boost::_bi::value<std::reference_wrapper<boost::asio::io_context> >, boost::_bi::value<std::reference_wrapper<agrpc::b::GrpcContext> > > >, boost::asio::any_io_executor, void>::complete<boost::asio::detail::binder1<boost::_bi::bind_t<void, void (*)(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&), boost::_bi::list<boost::arg<1>, boost::_bi::value<boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*>, boost::_bi::value<std::reference_wrapper<boost::asio::io_context> >, boost::_bi::value<std::reference_wrapper<agrpc::b::GrpcContext> > > >, boost::system::error_code> > (this=0x7ffe3dd219b8, function=..., handler=...)  
    at opensource/base/boost_1_86_0/boost/asio/detail/handler_work.hpp:470  
#35 0x000055bc6202f73d in boost::asio::detail::wait_handler<boost::_bi::bind_t<void, void (*)(boost::system::error_code const&, boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*, boost::asio::io_context&, agrpc::b::GrpcContext&), boost::_bi::list<boost::arg<1>, boost::_bi::value<boost::asio::basic_deadline_timer<boost::posix_time::ptime, boost::asio::time_traits<boost::posix_time::ptime>, boost::asio::any_io_executor>*>, boost::_bi::value<std::reference_wrapper<boost::asio::io_context> >, boost::_bi::value<std::reference_wrapper<agrpc::b::GrpcContext> > > >, boost::asio::any_io_executor>::do_complete (owner=0x55bc6362db80, base=0x55bc636afbe0) at opensource/base/boost_1_86_0/boost/asio/detail/wait_handler.hpp:76  
#36 0x000055bc6202cc0e in boost::asio::detail::scheduler_operation::complete (this=0x55bc636afbe0, owner=0x55bc6362db80, ec=..., bytes_transferred=0)  
    at opensource/base/boost_1_86_0/boost/asio/detail/scheduler_operation.hpp:40  
--Type <RET> for more, q to quit, c to continue without paging--  
#37 0x000055bc6202c9ba in boost::asio::detail::scheduler::do_run_one (this=0x55bc6362db80, lock=..., this_thread=..., ec=...) at opensource/base/boost_1_86_0/boost/asio/detail/impl/scheduler.ipp:493  
#38 0x000055bc6202affd in boost::asio::detail::scheduler::run (this=0x55bc6362db80, ec=...) at opensource/base/boost_1_86_0/boost/asio/detail/impl/scheduler.ipp:210  
#39 0x000055bc62020cfe in boost::asio::io_context::run (this=0x7ffe3dd22b28) at opensource/base/boost_1_86_0/boost/asio/impl/io_context.ipp:64  
  
```

---

## Comment 1

> Username: RavikumarTulugu  
> Created at: 2024-11-11 06:37:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2467352834  

just a bit of more information, the query in our case returns empty results.

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2024-11-11 06:50:45 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2467369642  

I am using mariadb server  on linux.   
The query in my case is like below    
SELECT * FROM room WHERE JSON_CONTAINS ( MEMBER_LIST, ? ) AND OWNER != ?   
The member list is an array of strings.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-11-11 09:49:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2467692991  

If the failure only happens sometimes, you may be violating [this precondition](https://www.boost.org/doc/libs/1_86_0/libs/mysql/doc/html/mysql/overview.html#mysql.overview.async). Remember that a single connection shouldn't be used to initiate any async operation until the one it's currently executing completes.  
  
To validate this hypothesis, you can add a flag that gets set when an operation starts and cleared when it finishes.  
  
If this is the case, you may need to introduce synchronization, maybe using a channel. Let me know if you need help with this.  
  
Let me know if it helps.  
  
Regards,  
Ruben.

---

## Comment 4

> Username: RavikumarTulugu  
> Created at: 2024-11-11 13:30:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2468186345  

okay let me verify and get back, thanks for the reply.

---

## Comment 5

> Username: RavikumarTulugu  
> Created at: 2024-11-11 15:19:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2468424439  

Thanks again for the insights, indeed the parallel execution was the case, do you have any sample code on this channel stuff ?

---

## Comment 6

> Username: anarthal  
> Created at: 2024-11-11 16:12:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2468539581  

This has come up before, so I hope I can implement #367 for Boost 1.88.  
  
You need an object that provides asynchronous mutual exclusion (an async mutex, if you prefer). With ideas taken from the first 2 examples in [this Asio page](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/examples/cpp20_examples.html):  
  
```cpp  
asio::awaitable<void> coro_main(  
    mysql::tcp_connection& conn,  
    asio::experimental::channel<void()>& lock  
)  
{  
    // Claim the lock by sending a message to the channel. Since the  
    // channel signature is void(), there are no arguments to send in the  
    // message itself.  
    co_await lock.async_send();  
  
    // Issue the SQL query to the server  
    const char* sql = "SELECT 'Hello world!'";  
    mysql::results result;  
    co_await conn.async_execute(sql, result);  
  
    // Release the lock by receiving the message back again.  
    lock.try_receive([](auto...) {});  
}  
```  
  
The channel must be created like this:  
  
```cpp  
asio::experimental::channel<void()> lock(ctx, 1); // ctx is your io_context  
```  
  
The idea is that you create a channel with a buffer size of 1 element. If the lock is free, the channel's buffer will be empty and `async_send` will succeed immediately. If it's not, the channel's buffer will be full, and `async_send` will block until the operation holding the lock releases it with `try_receive`.  
  
Some notes:  
  
* I wrote it using C++20 coroutines because it was easier, but you can use this method with callbacks, stackful coroutines or whatever completion style you're using.  
* The code I wrote is not exception-safe. If you're going down this path, you probably want a RAII wrapper for it (or something else if you're using callbacks).  
* Channels are experimental. I can probably write something for you using only timers (which are stable), but it may be less robust.  
  
Another option is using Boost.MySQL `connection_pool`, which implements similar semantics. If you only need 1 connection, you can still use it by setting `pool_params::max_size` to 1. You would get re-connection logic and pinging for free. The only caveat is that the feature is still experimental in 1.86, and has undergone some changes in 1.87 (currently in beta, will be released in some weeks), which makes it stable. If you're interested, let me know and I'll write something that avoids the breaking changes. Starting in 1.87, `connection_pool` should likely be what you want.  
  
Regards,  
Ruben.

---

## Comment 7

> Username: RavikumarTulugu  
> Created at: 2024-11-12 04:13:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2469557831  

To make the code clean and easily understandable, I would go with the connection pool. please point me to the patches if any so that i could apply them on my local tree and get the feature. Hope,  things are stable and robust.

---

## Comment 8

> Username: anarthal  
> Created at: 2024-11-12 09:28:30 UTC  
> Updated at: 2024-11-12 09:30:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/380#issuecomment-2470023811  

I'd recommend using the Boost 1.87 beta: https://archives.boost.io/beta/1.87.0.beta1/source/boost_1_87_0_b1_rc2.tar.gz  
It has the same structure as a regular release. There won't be any breaking changes from the beta to the release.  
  
This is how I'd use the pool:  
  
```cpp  
  
// In main  
// Pool configuration  
mysql::pool_params params {  
    .server_address = mysql::host_and_port("your-hostname"),  
    .username = "your-username",  
    .password = "your-password",  
    .database = "your-database",  
    .max_size = 1, // Set to the max number of connections you want. You probably want more than 1  
};  
  
// Create the pool  
mysql::connection_pool pool (ctx, std::move(params));  
  
// Run it. Only needed once per pool  
pool.async_run(asio::detached);  
  
// Call pool.cancel() to make run exit when your program  
// needs to quit, e.g. from a signal handler  
  
asio::awaitable<void> coro_main(  
    mysql::connection_pool& pool  
)  
{  
    // Get a connection. This is a proxy to an any_connection.  
    // If all connections are in use, this will wait until one is free.  
    // By default, this waits forever, so it may be adequate to set a timeout  
    // (maybe to the entire coroutine) using asio::cancel_after  
    mysql::pooled_connection conn = co_await pool.async_get_connection();  
  
    // Use it normally  
    mysql::results result;  
    co_await conn->async_execute("SELECT...", result);  
  
    // conn's destructor returns it to the pool  
}  
```  
  
Hope it helps.
