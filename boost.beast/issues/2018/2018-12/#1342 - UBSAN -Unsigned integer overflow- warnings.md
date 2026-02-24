# #1342 - UBSAN "Unsigned integer overflow" warnings [Closed]

> Username: firewave  
> Created at: 2018-12-03 13:27:09 UTC  
> Updated at: 2019-02-20 14:50:25 UTC  
> Closed at: 2019-02-20 14:50:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1342  

Most of these overflows don't seem to have any ill effect on the code beside triggering the (correct) UBSAN warning. I am using Boost 1.67.  
  
Unlike the other warnings this looks like it might actually have some effects on the execution in operator*():  
  
```  
Unsigned integer overflow: 2 - 1536 cannot be represented in type 'unsigned long'  
boost::beast::buffers_prefix_view<boost::beast::buffers_suffix>::const_iterator::operator++()  
boost::beast::detail::buffers_range_adaptor<boost::beast::buffers_prefix_view>::const_iterator::operator++()  
bool boost::beast::websocket::detail::utf8_checker_t<void>::write<boost::beast::buffers_prefix_view>(boost::beast::buffers_prefix_view<boost::beast::buffers_suffix> const&)  
boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::read_some_op<boost::asio::mutable_buffer, boost::beast::websocket::stream::read_op>::operator()(boost::system::error_code, unsigned long, bool)  
boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&, ...)  
void boost::beast::websocket::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::read_op<boost::beast::basic_flat_buffer, boost::asio::executor_binder>*)  
void boost::beast::websocket::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::read_some_op<boost::asio::mutable_buffer, boost::beast::websocket::stream::read_op>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::beast::websocket::stream::read_some_op>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::read_some_op<boost::asio::mutable_buffer, boost::beast::websocket::stream::read_op>&)  
void boost::asio::detail::asio_handler_invoke<boost::asio::detail::binder2, boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&)  
boost::asio::detail::executor_op<boost::asio::detail::binder2, std::allocator, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, ...)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::strand_executor_service::invoker, boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&)  
void boost::asio::io_context::executor_type::dispatch<boost::asio::detail::strand_executor_service::invoker, std::allocator>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&&, std::allocator<void> const&) const  
void boost::asio::detail::strand_executor_service::dispatch<boost::asio::io_context::executor_type const, boost::asio::detail::binder2, std::allocator>(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::executor_type const&, boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&)  
void boost::asio::strand<boost::asio::io_context::executor_type>::dispatch<boost::asio::detail::binder2, std::allocator>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&) const  
void boost::asio::detail::handler_work<boost::beast::websocket::stream::read_some_op, boost::asio::strand>::complete<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::websocket::stream::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::read_some_op<boost::asio::mutable_buffer, boost::beast::websocket::stream::read_op>&)  
boost::asio::detail::reactive_socket_recv_op<std::array, boost::beast::websocket::stream::read_some_op>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&)  
boost::asio::detail::scheduler::run(boost::system::error_code&)  
boost::asio::io_context::run()  
```   
  
Beast uses loop conditions which combine the condition with the increment (decrement in these cases) which causes warnings:  
  
```  
Unsigned integer overflow: 0 - 1 cannot be represented in type 'unsigned long'  
unsigned long boost::beast::detail::base64::encode<void>(void*, void const*, unsigned long)  
void boost::beast::websocket::detail::make_sec_ws_accept<void>(boost::beast::static_string<28ul, char, std::char_traits>&, boost::basic_string_view<char, std::char_traits>)  
boost::beast::http::message<false, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::build_response<boost::beast::http::basic_string_body, std::allocator, void (*)(boost::beast::http::message&)>(boost::beast::http::message<true, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> const&, void (* const&)(boost::beast::http::message<false, boost::beast::http::basic_string_body, boost::beast::http::basic_fields>&), boost::system::error_code&)  
boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::response_op<boost::asio::executor_binder>::data::data<boost::beast::http::basic_string_body, std::allocator, void (*)(boost::beast::http::message&)>(boost::asio::executor_binder<std::_Bind, boost::asio::strand> const&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>&, boost::beast::http::message<true, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> const&, void (* const&)(boost::beast::http::message<false, boost::beast::http::basic_string_body, boost::beast::http::basic_fields>&))  
void __gnu_cxx::new_allocator<boost::beast::websocket::stream::response_op::data>::construct<boost::beast::websocket::stream::response_op::data, boost::asio::executor_binder const&, boost::beast::websocket::stream&, boost::beast::http::message const&, void (*)(boost::beast::http::message&)>(boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::response_op<boost::asio::executor_binder>::data*, boost::asio::executor_binder<std::_Bind, boost::asio::strand> const&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>&, boost::beast::http::message<true, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> const&, void (*&&)(boost::beast::http::message<false, boost::beast::http::basic_string_body, boost::beast::http::basic_fields>&))  
void std::allocator_traits<std::allocator>::construct<boost::beast::websocket::stream::response_op::data, boost::asio::executor_binder const&, boost::beast::websocket::stream&, boost::beast::http::message const&, void (*)(boost::beast::http::message&)>(std::allocator<boost::beast::websocket::stream::response_op::data>&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::response_op<boost::asio::executor_binder>::data*, boost::asio::executor_binder<std::_Bind, boost::asio::strand> const&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>&, boost::beast::http::message<true, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> const&, void (*&&)(boost::beast::http::message<false, boost::beast::http::basic_string_body, boost::beast::http::basic_fields>&))  
boost::beast::handler_ptr<boost::beast::websocket::stream::response_op::data, boost::asio::executor_binder>::handler_ptr<boost::asio::executor_binder, boost::beast::websocket::stream&, boost::beast::http::message const&, void (*)(boost::beast::http::message&)>(boost::asio::executor_binder<std::_Bind, boost::asio::strand>&&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>&, boost::beast::http::message<true, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> const&, void (*&&)(boost::beast::http::message<false, boost::beast::http::basic_string_body, boost::beast::http::basic_fields>&))  
boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::response_op<boost::asio::executor_binder>::response_op<boost::asio::executor_binder, boost::beast::http::message const&, void (*)(boost::beast::http::message&)>(boost::asio::executor_binder<std::_Bind, boost::asio::strand>&&, boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>&, boost::beast::http::message<true, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> const&, void (*&&)(boost::beast::http::message<false, boost::beast::http::basic_string_body, boost::beast::http::basic_fields>&))  
boost::asio::async_result<std::decay::type, void (boost::system::error_code)>::return_type boost::beast::websocket::stream<boost::asio::basic_stream_socket, true>::async_accept<boost::beast::http::basic_string_body, std::allocator, boost::asio::executor_binder>(boost::beast::http::message<true, boost::beast::http::basic_string_body, boost::beast::http::basic_fields> const&, boost::asio::executor_binder<std::_Bind, boost::asio::strand>&&)  
```  
  
```  
Unsigned integer overflow: 0 - 1 cannot be represented in type 'unsigned long'  
bool boost::beast::detail::iequals<void>(boost::basic_string_view<char, std::char_traits>, boost::basic_string_view<char, std::char_traits>)  
boost::beast::iequals(boost::basic_string_view<char, std::char_traits>, boost::basic_string_view<char, std::char_traits>)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::do_field(boost::beast::http::field, boost::basic_string_view<char, std::char_traits>, boost::system::error_code&)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::parse_fields(char const*&, char const*, boost::system::error_code&)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::put(boost::asio::const_buffer const&, boost::system::error_code&)  
boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>::operator()(boost::system::error_code, unsigned long, bool)  
boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, ...)  
void boost::beast::http::detail::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::basic_string_body, std::allocator, boost::asio::executor_binder>*)  
void boost::beast::http::detail::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::beast::http::detail::read_some_op>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>&)  
void boost::asio::detail::asio_handler_invoke<boost::asio::detail::binder2, boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&)  
boost::asio::detail::executor_op<boost::asio::detail::binder2, std::allocator, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, ...)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::strand_executor_service::invoker, boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&)  
void boost::asio::io_context::executor_type::dispatch<boost::asio::detail::strand_executor_service::invoker, std::allocator>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&&, std::allocator<void> const&) const  
void boost::asio::detail::strand_executor_service::dispatch<boost::asio::io_context::executor_type const, boost::asio::detail::binder2, std::allocator>(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::executor_type const&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&)  
void boost::asio::strand<boost::asio::io_context::executor_type>::dispatch<boost::asio::detail::binder2, std::allocator>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&) const  
void boost::asio::detail::handler_work<boost::beast::http::detail::read_some_op, boost::asio::strand>::complete<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>&)  
boost::asio::detail::reactive_socket_recv_op<boost::asio::mutable_buffer, boost::beast::http::detail::read_some_op>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::epoll_reactor::descriptor_state::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&)  
boost::asio::detail::scheduler::run(boost::system::error_code&)  
boost::asio::io_context::run()  
```  
  
```  
Unsigned integer overflow: 0 - 1 cannot be represented in type 'unsigned long'  
bool boost::beast::detail::iequals<void>(boost::basic_string_view<char, std::char_traits>, boost::basic_string_view<char, std::char_traits>)  
boost::beast::iequals(boost::basic_string_view<char, std::char_traits>, boost::basic_string_view<char, std::char_traits>)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::do_field(boost::beast::http::field, boost::basic_string_view<char, std::char_traits>, boost::system::error_code&)::{lambda(boost::basic_string_view<char, std::char_traits> const&)#1}::operator()(boost::basic_string_view<char, std::char_traits> const&) const  
bool __gnu_cxx::__ops::_Iter_pred<boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>::operator()<boost::beast::http::token_list::const_iterator>(boost::beast::http::token_list::const_iterator)  
boost::beast::http::token_list::const_iterator std::__find_if<boost::beast::http::token_list::const_iterator, __gnu_cxx::__ops::_Iter_pred>(__gnu_cxx::__ops::_Iter_pred<boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>, __gnu_cxx::__ops::_Iter_pred<boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>, __gnu_cxx::__ops::_Iter_pred<boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>, std::input_iterator_tag)  
boost::beast::http::token_list::const_iterator std::__find_if<boost::beast::http::token_list::const_iterator, __gnu_cxx::__ops::_Iter_pred>(__gnu_cxx::__ops::_Iter_pred<boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>, __gnu_cxx::__ops::_Iter_pred<boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>, __gnu_cxx::__ops::_Iter_pred<boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>)  
boost::beast::http::token_list::const_iterator std::find_if<boost::beast::http::token_list::const_iterator, boost::beast::http::basic_parser::do_field(boost::beast::http::field, boost::basic_string_view, boost::system::error_code&)::{lambda(boost::basic_string_view const&)#1}>(boost::beast::http::basic_parser<true, boost::beast::http::parser>::do_field(boost::beast::http::field, boost::basic_string_view<char, std::char_traits>, boost::system::error_code&)::{lambda(boost::basic_string_view<char, std::char_traits> const&)#1}, boost::beast::http::basic_parser<true, boost::beast::http::parser>::do_field(boost::beast::http::field, boost::basic_string_view<char, std::char_traits>, boost::system::error_code&)::{lambda(boost::basic_string_view<char, std::char_traits> const&)#1}, boost::beast::http::basic_parser<true, boost::beast::http::parser>::do_field(boost::beast::http::field, boost::basic_string_view<char, std::char_traits>, boost::system::error_code&)::{lambda(boost::basic_string_view<char, std::char_traits> const&)#1})  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::do_field(boost::beast::http::field, boost::basic_string_view<char, std::char_traits>, boost::system::error_code&)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::parse_fields(char const*&, char const*, boost::system::error_code&)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::put(boost::asio::const_buffer const&, boost::system::error_code&)  
boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>::operator()(boost::system::error_code, unsigned long, bool)  
boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, ...)  
void boost::beast::http::detail::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::basic_string_body, std::allocator, boost::asio::executor_binder>*)  
void boost::beast::http::detail::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::beast::http::detail::read_some_op>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>&)  
void boost::asio::detail::asio_handler_invoke<boost::asio::detail::binder2, boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&)  
boost::asio::detail::executor_op<boost::asio::detail::binder2, std::allocator, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, ...)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::strand_executor_service::invoker, boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&)  
void boost::asio::io_context::executor_type::dispatch<boost::asio::detail::strand_executor_service::invoker, std::allocator>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&&, std::allocator<void> const&) const  
void boost::asio::detail::strand_executor_service::dispatch<boost::asio::io_context::executor_type const, boost::asio::detail::binder2, std::allocator>(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::executor_type const&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&)  
void boost::asio::strand<boost::asio::io_context::executor_type>::dispatch<boost::asio::detail::binder2, std::allocator>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&) const  
void boost::asio::detail::handler_work<boost::beast::http::detail::read_some_op, boost::asio::strand>::complete<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>&)  
boost::asio::detail::reactive_socket_recv_op<boost::asio::mutable_buffer, boost::beast::http::detail::read_some_op>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::epoll_reactor::descriptor_state::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&)  
boost::asio::detail::scheduler::run(boost::system::error_code&)  
boost::asio::io_context::run()  
```  
  
This also just appears to be a warning without any actual side effects  
  
```  
Unsigned integer overflow: 45 - 65 cannot be represented in type 'unsigned int'  
boost::beast::detail::ascii_tolower(char)  
boost::beast::http::detail::field_table::hash::operator()(boost::basic_string_view<char, std::char_traits>) const  
std::__detail::_Hash_code_base<boost::basic_string_view, std::pair, std::__detail::_Select1st, boost::beast::http::detail::field_table::hash, std::__detail::_Mod_range_hashing, std::__detail::_Default_ranged_hash, true>::_M_hash_code(boost::basic_string_view<char, std::char_traits> const&) const  
std::pair<std::__detail::_Node_iterator, bool> std::_Hashtable<boost::basic_string_view, std::pair, std::allocator, std::__detail::_Select1st, boost::beast::http::detail::field_table::iequal, boost::beast::http::detail::field_table::hash, std::__detail::_Mod_range_hashing, std::__detail::_Default_ranged_hash, std::__detail::_Prime_rehash_policy, std::__detail::_Hashtable_traits>::_M_emplace<boost::basic_string_view const&, boost::beast::http::field>(std::integral_constant<bool, true>, boost::basic_string_view<char, std::char_traits> const&, boost::beast::http::field&&)  
std::pair<std::__detail::_Node_iterator, bool> std::_Hashtable<boost::basic_string_view, std::pair, std::allocator, std::__detail::_Select1st, boost::beast::http::detail::field_table::iequal, boost::beast::http::detail::field_table::hash, std::__detail::_Mod_range_hashing, std::__detail::_Default_ranged_hash, std::__detail::_Prime_rehash_policy, std::__detail::_Hashtable_traits>::emplace<boost::basic_string_view const&, boost::beast::http::field>(boost::basic_string_view<char, std::char_traits> const&, boost::beast::http::field&&)  
std::pair<std::__detail::_Node_iterator, bool> std::unordered_map<boost::basic_string_view, boost::beast::http::field, boost::beast::http::detail::field_table::hash, boost::beast::http::detail::field_table::iequal, std::allocator>::emplace<boost::basic_string_view const&, boost::beast::http::field>(boost::basic_string_view<char, std::char_traits> const&, boost::beast::http::field&&)  
boost::beast::http::detail::field_table::field_table()  
boost::beast::http::detail::get_field_table()  
boost::beast::http::string_to_field(boost::basic_string_view<char, std::char_traits>)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::parse_fields(char const*&, char const*, boost::system::error_code&)  
boost::beast::http::basic_parser<true, boost::beast::http::parser>::put(boost::asio::const_buffer const&, boost::system::error_code&)  
boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>::operator()(boost::system::error_code, unsigned long, bool)  
boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, ...)  
void boost::beast::http::detail::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::basic_string_body, std::allocator, boost::asio::executor_binder>*)  
void boost::beast::http::detail::asio_handler_invoke<boost::asio::detail::binder2&>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::beast::http::detail::read_some_op>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>&)  
void boost::asio::detail::asio_handler_invoke<boost::asio::detail::binder2, boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>*)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::binder2, boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&)  
boost::asio::detail::executor_op<boost::asio::detail::binder2, std::allocator, boost::asio::detail::scheduler_operation>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>::operator()()  
void boost::asio::asio_handler_invoke<boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, ...)  
void boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::strand_executor_service::invoker, boost::asio::detail::strand_executor_service::invoker>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&)  
void boost::asio::io_context::executor_type::dispatch<boost::asio::detail::strand_executor_service::invoker, std::allocator>(boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>&&, std::allocator<void> const&) const  
void boost::asio::detail::strand_executor_service::dispatch<boost::asio::io_context::executor_type const, boost::asio::detail::binder2, std::allocator>(std::shared_ptr<boost::asio::detail::strand_executor_service::strand_impl> const&, boost::asio::io_context::executor_type const&, boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&)  
void boost::asio::strand<boost::asio::io_context::executor_type>::dispatch<boost::asio::detail::binder2, std::allocator>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&&, std::allocator<void> const&) const  
void boost::asio::detail::handler_work<boost::beast::http::detail::read_some_op, boost::asio::strand>::complete<boost::asio::detail::binder2>(boost::asio::detail::binder2<boost::beast::http::detail::read_some_op, boost::system::error_code, unsigned long>&, boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket, boost::beast::basic_flat_buffer, true, boost::beast::http::parser, boost::beast::http::detail::read_msg_op>&)  
boost::asio::detail::reactive_socket_recv_op<boost::asio::mutable_buffer, boost::beast::http::detail::read_some_op>::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::epoll_reactor::descriptor_state::do_complete(void*, boost::asio::detail::scheduler_operation*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler_operation::complete(void*, boost::system::error_code const&, unsigned long)  
boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&)  
boost::asio::detail::scheduler::run(boost::system::error_code&)  
boost::asio::io_context::run()  
cppbroker::IoContextPool::run()::{lambda()#1}::operator()() const  
void std::__invoke_impl<void, cppbroker::IoContextPool::run()::{lambda()#1}>(std::__invoke_other, cppbroker::IoContextPool::run()::{lambda()#1}&&)  
std::__invoke_result<cppbroker::IoContextPool::run()::{lambda()#1}>::type std::__invoke<cppbroker::IoContextPool::run()::{lambda()#1}>(std::__invoke_result&&, (cppbroker::IoContextPool::run()::{lambda()#1}&&)...)  
_ZNSt6thread8_InvokerISt5tupleIJZN9cppbroker13IoContextPool3runEvEUlvE_EEE9_M_invokeIJLm0EEEEDTclsr3stdE8__invokespcl10_S_declvalIXT_EEEEESt12_Index_tupleIJXspT_EEE  
std::thread::_Invoker<std::tuple>::operator()()  
std::thread::_State_impl<std::thread::_Invoker>::_M_run()  
(libstdc++.so.6+0xbbafe)  
start_thread  
clone  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-03 16:58:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-443784346  

These look like false positives

---

## Comment 2

> Username: firewave  
> Created at: 2018-12-04 09:10:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-444025639  

I wouldn't call these "false positives", but "by design". "0 with post decrement" will properly exit the loop, but will cause the value to overflow. It actually found some valid cases in the code around the Beast usage.  
  
I am still wondering what would happen in `boost::beast::buffers_prefix_view` with the usage of  `_remain` in `operator*()` it after it overflowed.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-04 13:40:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-444102873  

> It actually found some valid cases in the code around the Beast usage.  
  
Can you please provide a link to beast code which you believe is incorrect or contains a defect?

---

## Comment 4

> Username: firewave  
> Created at: 2018-12-22 18:15:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-449588787  

I currently out sick and will look into this in the new year.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-12-22 20:26:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-449595705  

Ah, I think you are referring to this line  
https://github.com/boostorg/beast/blob/f00237cb35bcb0d3d544db4b80de38f1aba78ab0/include/boost/beast/core/impl/buffers_prefix.hpp#L154  
  
Working as intended :)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-01-13 01:55:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-453795359  

Has this been resolved?

---

## Comment 7

> Username: firewave  
> Created at: 2019-01-14 14:26:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-454022495  

Still haven't looked into it. Had to take care of more pressing things...

---

## Comment 8

> Username: stale[bot]  
> Created at: 2019-02-13 14:43:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-463224566  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2019-02-20 14:50:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1342#issuecomment-465608419  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
