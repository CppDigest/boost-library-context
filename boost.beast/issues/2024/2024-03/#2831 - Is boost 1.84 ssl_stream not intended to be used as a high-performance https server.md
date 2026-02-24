# #2831 - Is boost 1.84 ssl_stream not intended to be used as a high-performance https server? [Closed]

> Username: hoyhoy  
> Created at: 2024-03-04 01:39:37 UTC  
> Updated at: 2024-05-06 08:04:28 UTC  
> Closed at: 2024-05-06 08:04:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2831  

*Boost 1.84*  
  
`libs/beast/example/http/server/async-ssl/http_server_async_ssl.cpp` appears to have a number of issues.  
  
(1) Seems to not be thread-safe...  
  
```  
# server...  
  
 ulimit -n 100000 && ./http_server_async_ssl 127.0.0.1 9092 . 17  
  
WARNING: ThreadSanitizer: data race (pid=91199)  
  Read of size 8 at 0x00010edfcad8 by thread T2:  
    #0 std::__1::ios_base::width[abi:ue170006]() const <null> (a.out:x86_64+0x10018a66d)  
    boostorg/boost#1 std::__1::ostreambuf_iterator<char, std::__1::char_traits<char>> std::__1::__pad_and_output[abi:ue170006]<char, std::__1::char_traits<char>>(std::__1::ostreambuf_iterator<char, std::__1::char_traits<char>>, char const*, char const*, char const*, std::__1::ios_base&, char) <null> (a.out:x86_64+0x10018a1a3)  
    boostorg/boost#2 std::__1::basic_ostream<char, std::__1::char_traits<char>>& std::__1::__put_character_sequence[abi:ue170006]<char, std::__1::char_traits<char>>(std::__1::basic_ostream<char, std::__1::char_traits<char>>&, char const*, unsigned long) <null> (a.out:x86_64+0x100189f33)  
    boostorg/boost#3 std::__1::basic_ostream<char, std::__1::char_traits<char>>& std::__1::operator<<[abi:ue170006]<std::__1::char_traits<char>>(std::__1::basic_ostream<char, std::__1::char_traits<char>>&, char const*) <null> (a.out:x86_64+0x100002a6d)  
    boostorg/boost#4 fail(boost::system::error_code, char const*) <null> (a.out:x86_64+0x1000027c8)  
    boostorg/boost#5 session::on_handshake(boost::system::error_code) <null> (a.out:x86_64+0x100059822)  
    boostorg/boost#6 decltype(*std::declval<std::__1::shared_ptr<session>>().*std::declval<void (session::* const&)(boost::system::error_code)>()(std::declval<boost::system::error_code const&>())) std::__1::__invoke[abi:ue170006]<void (session::* const&)(boost::system::error_code), std::__1::shared_ptr<session>, boost::system::error_code const&, void>(void (session::* const&)(boost::system::error_code), std::__1::shared_ptr<session>&&, boost::system::error_code const&) <null> (a.out:x86_64+0x10008dc1c)  
    boostorg/boost#7 std::__1::__invoke_return<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>, boost::system::error_code const&>::type std::__1::__mem_fn<void (session::*)(boost::system::error_code)>::operator()[abi:ue170006]<std::__1::shared_ptr<session>, boost::system::error_code const&>(std::__1::shared_ptr<session>&&, boost::system::error_code const&) const <null> (a.out:x86_64+0x10008da15)  
    boostorg/boost#8 void boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>::invoke<0ul, boost::system::error_code const&>(std::__1::integral_constant<bool, true>, boost::mp11::integer_sequence<unsigned long, 0ul>, boost::system::error_code const&) <null> (a.out:x86_64+0x10008d92a)  
    boostorg/boost#9 void boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>::operator()<boost::system::error_code const&>(boost::system::error_code const&) <null> (a.out:x86_64+0x10008d885)  
    boostorg/boost#10 void boost::asio::ssl::detail::handshake_op::call_handler<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>(boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>&, boost::system::error_code const&, unsigned long const&) const <null> (a.out:x86_64+0x10005c8dd)  
    boostorg/boost#11 boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>::operator()(boost::system::error_code, unsigned long, int) <null> (a.out:x86_64+0x10005b22e)  
    boostorg/boost#12 void boost::beast::async_base<boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>, boost::asio::any_io_executor, std::__1::allocator<void>>::complete_now<boost::system::error_code&, unsigned long&>(boost::system::error_code&, unsigned long&) <null> (a.out:x86_64+0x100063ce1)  
    boostorg/boost#13 boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>::operator()(boost::system::error_code, unsigned long) <null> (a.out:x86_64+0x10005eed4)  
    boostorg/boost#14 boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>::operator()() <null> (a.out:x86_64+0x100065deb)  
    boostorg/boost#15 boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>::operator()() <null> (a.out:x86_64+0x10006aaf5)  
    boostorg/boost#16 void boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>, std::__1::allocator<void>>(boost::asio::detail::executor_function::impl_base*, bool) <null> (a.out:x86_64+0x10006b01b)  
    boostorg/boost#17 boost::asio::detail::executor_function::operator()() <null> (a.out:x86_64+0x100023a5a)  
    boostorg/boost#18 void boost::asio::detail::strand_executor_service::do_execute<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const, boost::asio::detail::executor_function, std::__1::allocator<void>>(std::__1::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const&, boost::asio::detail::executor_function&&, std::__1::allocator<void> const&) <null> (a.out:x86_64+0x10002b976)  
    boostorg/boost#19 void boost::asio::detail::strand_executor_service::execute<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const, boost::asio::detail::executor_function>(std::__1::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const&, boost::asio::detail::executor_function&&, std::__1::enable_if<can_query<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const, boost::asio::execution::allocator_t<void>>::value, void>::type*) <null> (a.out:x86_64+0x10002b840)  
    boostorg/boost#20 boost::asio::constraint<traits::execute_member<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const&, boost::asio::detail::executor_function>::is_valid, void>::type boost::asio::strand<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul>>::execute<boost::asio::detail::executor_function>(boost::asio::detail::executor_function&&) const <null> (a.out:x86_64+0x10002b790)  
    boostorg/boost#21 void boost::asio::execution::detail::any_executor_base::execute_ex<boost::asio::strand<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul>>>(boost::asio::execution::detail::any_executor_base const&, boost::asio::detail::executor_function&&) <null> (a.out:x86_64+0x10002b55f)  
    boostorg/boost#22 void boost::asio::execution::detail::any_executor_base::execute<boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>>(boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>&&) const <null> (a.out:x86_64+0x10006a63c)  
    boostorg/boost#23 boost::asio::detail::work_dispatcher<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>, boost::asio::any_io_executor, void>::operator()() <null> (a.out:x86_64+0x10006a3a1)  
    boostorg/boost#24 void boost::asio::detail::executor_function::complete<boost::asio::detail::work_dispatcher<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>, boost::asio::any_io_executor, void>, std::__1::allocator<void>>(boost::asio::detail::executor_function::impl_base*, bool) <null> (a.out:x86_64+0x10006b98b)  
    boostorg/boost#25 boost::asio::detail::executor_function::operator()() <null> (a.out:x86_64+0x100023a5a)  
    boostorg/boost#26 boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::__1::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x1000248e6)  
    boostorg/boost#27 boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x100014b67)  
    boostorg/boost#28 boost::asio::detail::strand_executor_service::run_ready_handlers(std::__1::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl>&) <null> (a.out:x86_64+0x100025295)  
    boostorg/boost#29 boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0ul> const, void>::operator()() <null> (a.out:x86_64+0x100024cc9)  
    boostorg/boost#30 boost::asio::detail::executor_op<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0ul> const, void>, boost::asio::detail::recycling_allocator<void, boost::asio::detail::thread_info_base::default_tag>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x100026697)  
    boostorg/boost#31 boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x100014b67)  
    boostorg/boost#32 boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) <null> (a.out:x86_64+0x100013de2)  
    boostorg/boost#33 boost::asio::detail::scheduler::run(boost::system::error_code&) <null> (a.out:x86_64+0x1000137b5)  
    boostorg/boost#34 boost::asio::io_context::run() <null> (a.out:x86_64+0x1000039b2)  
    boostorg/boost#35 main::$_0::operator()() const <null> (a.out:x86_64+0x1001992b5)  
    boostorg/boost#36 decltype(std::declval<main::$_0>()()) std::__1::__invoke[abi:ue170006]<main::$_0>(main::$_0&&) <null> (a.out:x86_64+0x100199215)  
    boostorg/boost#37 void std::__1::__thread_execute[abi:ue170006]<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct>>, main::$_0>(std::__1::tuple<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct>>, main::$_0>&, std::__1::__tuple_indices<>) <null> (a.out:x86_64+0x10019915d)  
    boostorg/boost#38 void* std::__1::__thread_proxy[abi:ue170006]<std::__1::tuple<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct>>, main::$_0>>(void*) <null> (a.out:x86_64+0x100198622)  
  
  Previous write of size 8 at 0x00010edfcad8 by thread T12:  
    #0 std::__1::ios_base::width[abi:ue170006](long) <null> (a.out:x86_64+0x10018a846)  
    boostorg/boost#1 std::__1::ostreambuf_iterator<char, std::__1::char_traits<char>> std::__1::__pad_and_output[abi:ue170006]<char, std::__1::char_traits<char>>(std::__1::ostreambuf_iterator<char, std::__1::char_traits<char>>, char const*, char const*, char const*, std::__1::ios_base&, char) <null> (a.out:x86_64+0x10018a385)  
    boostorg/boost#2 std::__1::basic_ostream<char, std::__1::char_traits<char>>& std::__1::__put_character_sequence[abi:ue170006]<char, std::__1::char_traits<char>>(std::__1::basic_ostream<char, std::__1::char_traits<char>>&, char const*, unsigned long) <null> (a.out:x86_64+0x100189f33)  
    boostorg/boost#3 std::__1::basic_ostream<char, std::__1::char_traits<char>>& std::__1::operator<<[abi:ue170006]<std::__1::char_traits<char>>(std::__1::basic_ostream<char, std::__1::char_traits<char>>&, char const*) <null> (a.out:x86_64+0x100002a6d)  
    boostorg/boost#4 fail(boost::system::error_code, char const*) <null> (a.out:x86_64+0x100002822)  
    boostorg/boost#5 session::on_handshake(boost::system::error_code) <null> (a.out:x86_64+0x100059822)  
    boostorg/boost#6 decltype(*std::declval<std::__1::shared_ptr<session>>().*std::declval<void (session::* const&)(boost::system::error_code)>()(std::declval<boost::system::error_code const&>())) std::__1::__invoke[abi:ue170006]<void (session::* const&)(boost::system::error_code), std::__1::shared_ptr<session>, boost::system::error_code const&, void>(void (session::* const&)(boost::system::error_code), std::__1::shared_ptr<session>&&, boost::system::error_code const&) <null> (a.out:x86_64+0x10008dc1c)  
    boostorg/boost#7 std::__1::__invoke_return<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>, boost::system::error_code const&>::type std::__1::__mem_fn<void (session::*)(boost::system::error_code)>::operator()[abi:ue170006]<std::__1::shared_ptr<session>, boost::system::error_code const&>(std::__1::shared_ptr<session>&&, boost::system::error_code const&) const <null> (a.out:x86_64+0x10008da15)  
    boostorg/boost#8 void boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>::invoke<0ul, boost::system::error_code const&>(std::__1::integral_constant<bool, true>, boost::mp11::integer_sequence<unsigned long, 0ul>, boost::system::error_code const&) <null> (a.out:x86_64+0x10008d92a)  
    boostorg/boost#9 void boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>::operator()<boost::system::error_code const&>(boost::system::error_code const&) <null> (a.out:x86_64+0x10008d885)  
    boostorg/boost#10 void boost::asio::ssl::detail::handshake_op::call_handler<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>(boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>&, boost::system::error_code const&, unsigned long const&) const <null> (a.out:x86_64+0x10005c8dd)  
    boostorg/boost#11 boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>::operator()(boost::system::error_code, unsigned long, int) <null> (a.out:x86_64+0x10005b22e)  
    boostorg/boost#12 void boost::beast::async_base<boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>, boost::asio::any_io_executor, std::__1::allocator<void>>::complete_now<boost::system::error_code&, unsigned long&>(boost::system::error_code&, unsigned long&) <null> (a.out:x86_64+0x100063ce1)  
    boostorg/boost#13 boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>::operator()(boost::system::error_code, unsigned long) <null> (a.out:x86_64+0x10005eed4)  
    boostorg/boost#14 boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>::operator()() <null> (a.out:x86_64+0x100065deb)  
    boostorg/boost#15 boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>::operator()() <null> (a.out:x86_64+0x10006aaf5)  
    boostorg/boost#16 void boost::asio::detail::executor_function::complete<boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>, std::__1::allocator<void>>(boost::asio::detail::executor_function::impl_base*, bool) <null> (a.out:x86_64+0x10006b01b)  
    boostorg/boost#17 boost::asio::detail::executor_function::operator()() <null> (a.out:x86_64+0x100023a5a)  
    boostorg/boost#18 void boost::asio::detail::strand_executor_service::do_execute<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const, boost::asio::detail::executor_function, std::__1::allocator<void>>(std::__1::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const&, boost::asio::detail::executor_function&&, std::__1::allocator<void> const&) <null> (a.out:x86_64+0x10002b976)  
    boostorg/boost#19 void boost::asio::detail::strand_executor_service::execute<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const, boost::asio::detail::executor_function>(std::__1::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const&, boost::asio::detail::executor_function&&, std::__1::enable_if<can_query<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const, boost::asio::execution::allocator_t<void>>::value, void>::type*) <null> (a.out:x86_64+0x10002b840)  
    boostorg/boost#20 boost::asio::constraint<traits::execute_member<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul> const&, boost::asio::detail::executor_function>::is_valid, void>::type boost::asio::strand<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul>>::execute<boost::asio::detail::executor_function>(boost::asio::detail::executor_function&&) const <null> (a.out:x86_64+0x10002b790)  
    boostorg/boost#21 void boost::asio::execution::detail::any_executor_base::execute_ex<boost::asio::strand<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 4ul>>>(boost::asio::execution::detail::any_executor_base const&, boost::asio::detail::executor_function&&) <null> (a.out:x86_64+0x10002b55f)  
    boostorg/boost#22 void boost::asio::execution::detail::any_executor_base::execute<boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>>(boost::asio::detail::binder0<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>>&&) const <null> (a.out:x86_64+0x10006a63c)  
    boostorg/boost#23 boost::asio::detail::work_dispatcher<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>, boost::asio::any_io_executor, void>::operator()() <null> (a.out:x86_64+0x10006a3a1)  
    boostorg/boost#24 void boost::asio::detail::executor_function::complete<boost::asio::detail::work_dispatcher<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<true, boost::asio::mutable_buffers_1, boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::asio::ssl::detail::handshake_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code), std::__1::shared_ptr<session>>>>, boost::system::error_code, unsigned long>, boost::asio::any_io_executor, void>, std::__1::allocator<void>>(boost::asio::detail::executor_function::impl_base*, bool) <null> (a.out:x86_64+0x10006b98b)  
    boostorg/boost#25 boost::asio::detail::executor_function::operator()() <null> (a.out:x86_64+0x100023a5a)  
    boostorg/boost#26 boost::asio::detail::executor_op<boost::asio::detail::executor_function, std::__1::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x1000248e6)  
    boostorg/boost#27 boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x100014b67)  
    boostorg/boost#28 boost::asio::detail::strand_executor_service::run_ready_handlers(std::__1::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl>&) <null> (a.out:x86_64+0x100025295)  
    boostorg/boost#29 boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0ul> const, void>::operator()() <null> (a.out:x86_64+0x100024cc9)  
    boostorg/boost#30 boost::asio::detail::executor_op<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::basic_executor_type<std::__1::allocator<void>, 0ul> const, void>, boost::asio::detail::recycling_allocator<void, boost::asio::detail::thread_info_base::default_tag>, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x100026697)  
    boostorg/boost#31 boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long) <null> (a.out:x86_64+0x100014b67)  
    boostorg/boost#32 boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) <null> (a.out:x86_64+0x100013de2)  
    boostorg/boost#33 boost::asio::detail::scheduler::run(boost::system::error_code&) <null> (a.out:x86_64+0x1000137b5)  
    boostorg/boost#34 boost::asio::io_context::run() <null> (a.out:x86_64+0x1000039b2)  
    boostorg/boost#35 main::$_0::operator()() const <null> (a.out:x86_64+0x1001992b5)  
    boostorg/boost#36 decltype(std::declval<main::$_0>()()) std::__1::__invoke[abi:ue170006]<main::$_0>(main::$_0&&) <null> (a.out:x86_64+0x100199215)  
    boostorg/boost#37 void std::__1::__thread_execute[abi:ue170006]<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct>>, main::$_0>(std::__1::tuple<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct>>, main::$_0>&, std::__1::__tuple_indices<>) <null> (a.out:x86_64+0x10019915d)  
    boostorg/boost#38 void* std::__1::__thread_proxy[abi:ue170006]<std::__1::tuple<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct>>, main::$_0>>(void*) <null> (a.out:x86_64+0x100198622)  
  
  Location is global 'std::__1::cerr' of size 160 at 0x00010edfcab8 (libc++.1.0.dylib+0xb2ad8)  
  
  Thread T2 (tid=901103, running) created by main thread at:  
    #0 pthread_create <null> (libclang_rt.tsan_osx_dynamic.dylib:x86_64+0x2fc8f)  
    boostorg/boost#1 std::__1::__libcpp_thread_create[abi:ue170006](_opaque_pthread_t**, void* (*)(void*), void*) <null> (a.out:x86_64+0x100198579)  
    boostorg/boost#2 std::__1::thread::thread<main::$_0, void>(main::$_0&&) <null> (a.out:x86_64+0x100198342)  
    boostorg/boost#3 std::__1::thread::thread<main::$_0, void>(main::$_0&&) <null> (a.out:x86_64+0x100198265)  
    boostorg/boost#4 void std::__1::allocator<std::__1::thread>::construct[abi:ue170006]<std::__1::thread, main::$_0>(std::__1::thread*, main::$_0&&) <null> (a.out:x86_64+0x100198201)  
    boostorg/boost#5 void std::__1::allocator_traits<std::__1::allocator<std::__1::thread>>::construct[abi:ue170006]<std::__1::thread, main::$_0, void>(std::__1::allocator<std::__1::thread>&, std::__1::thread*, main::$_0&&) <null> (a.out:x86_64+0x100198075)  
    boostorg/boost#6 void std::__1::vector<std::__1::thread, std::__1::allocator<std::__1::thread>>::__construct_one_at_end[abi:ue170006]<main::$_0>(main::$_0&&) <null> (a.out:x86_64+0x100197dd9)  
    boostorg/boost#7 std::__1::thread& std::__1::vector<std::__1::thread, std::__1::allocator<std::__1::thread>>::emplace_back<main::$_0>(main::$_0&&) <null> (a.out:x86_64+0x100003904)  
    boostorg/boost#8 main <null> (a.out:x86_64+0x100002ef1)  
  
  Thread T12 (tid=901113, running) created by main thread at:  
    #0 pthread_create <null> (libclang_rt.tsan_osx_dynamic.dylib:x86_64+0x2fc8f)  
    boostorg/boost#1 std::__1::__libcpp_thread_create[abi:ue170006](_opaque_pthread_t**, void* (*)(void*), void*) <null> (a.out:x86_64+0x100198579)  
    boostorg/boost#2 std::__1::thread::thread<main::$_0, void>(main::$_0&&) <null> (a.out:x86_64+0x100198342)  
    boostorg/boost#3 std::__1::thread::thread<main::$_0, void>(main::$_0&&) <null> (a.out:x86_64+0x100198265)  
    boostorg/boost#4 void std::__1::allocator<std::__1::thread>::construct[abi:ue170006]<std::__1::thread, main::$_0>(std::__1::thread*, main::$_0&&) <null> (a.out:x86_64+0x100198201)  
    boostorg/boost#5 void std::__1::allocator_traits<std::__1::allocator<std::__1::thread>>::construct[abi:ue170006]<std::__1::thread, main::$_0, void>(std::__1::allocator<std::__1::thread>&, std::__1::thread*, main::$_0&&) <null> (a.out:x86_64+0x100198075)  
    boostorg/boost#6 void std::__1::vector<std::__1::thread, std::__1::allocator<std::__1::thread>>::__construct_one_at_end[abi:ue170006]<main::$_0>(main::$_0&&) <null> (a.out:x86_64+0x100197dd9)  
    boostorg/boost#7 std::__1::thread& std::__1::vector<std::__1::thread, std::__1::allocator<std::__1::thread>>::emplace_back<main::$_0>(main::$_0&&) <null> (a.out:x86_64+0x100003904)  
    boostorg/boost#8 main <null> (a.out:x86_64+0x100002ef1)  
  
SUMMARY: ThreadSanitizer: data race (/Users/jperrie/ssl_server/a.out:x86_64+0x10018a66d) in std::__1::ios_base::width[abi:ue170006]() const  
```  
  
(2) Running with a number of concurrent sessions causes it to fail.  
  
```  
# client...  
[error] SSL_write() failed (syscall): Broken pipe  
[error] SSL_write() failed (syscall): Broken pipe  
HTTP/1.1 200     0.41 secs:     891 bytes ==> GET  /CMakeLists.txt  
[error] Failed to make an SSL connection: 5: Broken pipe  
HTTP/1.1 200     0.41 secs:     891 bytes ==> GET  /CMakeLists.txt  
[error] SSL_write() failed (syscall): Broken pipe  
[error] Failed to make an SSL connection: 5: Broken pipe  
HTTP/1.1 200     0.40 secs:     891 bytes ==> GET  /CMakeLists.txt  
HTTP/1.1 200     0.51 secs:     891 bytes ==> GET  /CMakeLists.txt  
HTTP/1.1 200     0.41 secs:     891 bytes ==> GET  /CMakeLists.txt  
HTTP/1.1 200     0.51 secs:     891 bytes ==> GET  /CMakeLists.txt  
```  
  
```  
# beast example ssl server...  
  
 ulimit -n 100000 && ./http_server_async_ssl 127.0.0.1 9092 . 17  
  
  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
handshake: Connection reset by peer  
```  
  
My current ssl implementation is suffering from a number of the same issues.  Is boost 1.84 ssl_stream not meant to be used as a high-performance https server?  
  
```  
Transactions:		     2610    hits  
Availability:		       71.45 %  
Elapsed time:		       14.17 secs  
Data transferred:	        2.22 MB  
Response time:		      516.77 ms  
Transaction rate:	      184.19 trans/sec  
Throughput:		        0.16 MB/sec  
Concurrency:		       95.18  
Successful transactions:     2610  
Failed transactions:	     1043  
Longest transaction:	    13130.00 ms  
Shortest transaction:	       40.00 ms  
```

---

## Comment 1

> Username: sehe  
> Created at: 2024-03-04 05:16:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2831#issuecomment-1975749072  

I cannot reproduce that with current Beast, running both with tsan and without I get clean results, e.g.  
  
```  
ab -k -c 8 -n 10000 https://0.0.0.0:8080/main.cpp   
Benchmarking 0.0.0.0 (be patient)  
Completed 1000 requests  
Completed 2000 requests  
Completed 3000 requests  
Completed 4000 requests  
Completed 5000 requests  
Completed 6000 requests  
Completed 7000 requests  
Completed 8000 requests  
Completed 9000 requests  
Completed 10000 requests  
Finished 10000 requests  
  
  
Server Software:        Boost.Beast/351  
Server Hostname:        0.0.0.0  
Server Port:            8080  
SSL/TLS Protocol:       TLSv1.2,ECDHE-RSA-AES256-GCM-SHA384,2048,256  
Server Temp Key:        X25519 253 bits  
  
Document Path:          /main.cpp  
Document Length:        5798 bytes  
  
Concurrency Level:      8  
Time taken for tests:   55.907 seconds  
Complete requests:      10000  
Failed requests:        0  
Keep-Alive requests:    10000  
Total transferred:      59200000 bytes  
HTML transferred:       57980000 bytes  
Requests per second:    178.87 [#/sec] (mean)  
Time per request:       44.725 [ms] (mean)  
Time per request:       5.591 [ms] (mean, across all concurrent requests)  
Transfer rate:          1034.09 [Kbytes/sec] received  
  
Connection Times (ms)  
              min  mean[+/-sd] median   max  
Connect:        0    0   0.3      0      12  
Processing:    42   45   1.5     44      52  
Waiting:        0    1   0.2      1       4  
Total:         43   45   1.5     44      58  
  
Percentage of the requests served within a certain time (ms)  
  50%     44  
  66%     44  
  75%     44  
  80%     44  
  90%     48  
  95%     48  
  98%     48  
  99%     48  
```   
  
and not a peep from the thread sanitizer. Can you provide specifics about your steps to build (did you use b2/Jamfile or cmake? what OS, compiler, openssl versions?). Specifically, it is possible to compile with compiler defines that tell Boost, Asio or Beast to disable feature like thread safety (or thread support in general).

---

## Comment 2

> Username: ashtum  
> Created at: 2024-03-04 07:27:13 UTC  
> Updated at: 2024-03-04 07:29:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2831#issuecomment-1975888900  

@hoyhoy Regarding the performance, make sure you are building the application in release mode. Boost.Beast and Boost.Asio have a lot of indirection and generic functions, which result in very slow debug executable.  
  
If you build in release mode, you can expect around 100K req/s on a single thread and around 200K req/s with 4 threads (with small responses):  
```BASH  
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>  
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/  
Licensed to The Apache Software Foundation, http://www.apache.org/  
  
Benchmarking 0.0.0.0 (be patient)  
Completed 10000 requests  
Completed 20000 requests  
Completed 30000 requests  
Completed 40000 requests  
Completed 50000 requests  
Completed 60000 requests  
Completed 70000 requests  
Completed 80000 requests  
Completed 90000 requests  
Completed 100000 requests  
Finished 100000 requests  
  
  
Server Software:        Boost.Beast/353  
Server Hostname:        0.0.0.0  
Server Port:            8080  
SSL/TLS Protocol:       TLSv1.2,ECDHE-RSA-AES256-GCM-SHA384,2048,256  
Server Temp Key:        X25519 253 bits  
  
Document Path:          /file  
Document Length:        1024 bytes  
  
Concurrency Level:      100  
Time taken for tests:   0.477 seconds  
Complete requests:      100000  
Failed requests:        0  
Keep-Alive requests:    100000  
Total transferred:      114600000 bytes  
HTML transferred:       102400000 bytes  
Requests per second:    209536.42 [#/sec] (mean)  
Time per request:       0.477 [ms] (mean)  
Time per request:       0.005 [ms] (mean, across all concurrent requests)  
Transfer rate:          234500.72 [Kbytes/sec] received  
  
Connection Times (ms)  
              min  mean[+/-sd] median   max  
Connect:        0    0   1.9      0      66  
Processing:     0    0   0.4      0      28  
Waiting:        0    0   0.4      0      28  
Total:          0    0   2.0      0      67  
  
Percentage of the requests served within a certain time (ms)  
  50%      0  
  66%      0  
  75%      0  
  80%      0  
  90%      0  
  95%      0  
  98%      1  
  99%      1  
 100%     67 (longest request)  
```

---

## Comment 3

> Username: hoyhoy  
> Created at: 2024-03-04 11:02:16 UTC  
> Updated at: 2024-03-04 18:44:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2831#issuecomment-1976322297  

I'm obviously running it with the clang thread-sanitizer enabled, so it's a `-glldb` binary.  I was testing it with more ab threads than hardware-concurrency since our SSL app is crashing in that case.  It seems like bad SSL requests cause thread-safety issues in the latest boost 1.84 example implementation as well.  That's what we're seeing in production.

---

## Comment 4

> Username: ashtum  
> Created at: 2024-03-04 11:40:42 UTC  
> Updated at: 2024-03-04 13:00:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2831#issuecomment-1976389963  

You are correct; access to `std::cerr` is not thread-safe, resulting in a ThreadSanitizer warning. If you are using C++ 20, you can encapsulate `std::cerr` within a `std::osyncstream`.  
Please keep this issue open, as it needs to be addressed in all examples.

---

## Comment 5

> Username: hoyhoy  
> Created at: 2024-03-04 18:14:41 UTC  
> Updated at: 2024-03-09 19:04:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2831#issuecomment-1977183900  

It's more than that though, the ssl errors aren't handled correctly in the example.  There's really no canonical documentation or examples for recovering from ssl errors like truncated reads and timeouts due to the server being overloaded.  We've just spent weeks debugging thread-safety and error-handling issues with our implementation, and it's still not working.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2024-03-04 18:30:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2831#issuecomment-1977209552  

Is your i/o context multithreaded and are you using strands?

---

## Comment 7

> Username: hoyhoy  
> Created at: 2024-03-04 18:38:57 UTC  
> Updated at: 2024-03-04 18:41:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2831#issuecomment-1977222366  

> Is your i/o context multithreaded and are you using strands?  
  
Yes, but I'm sure we're doing it wrong.    
  
It was originally implemented based on [http/server3](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/example/cpp11/http/server3/server.cpp) to replace mongoose.  The example http_server_async_ssl.cpp server in boost 1.84 doesn't have the SSL disconnection crashes that we're seeing, but also it doesn't handle truncated-reads, etc.
