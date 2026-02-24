# #2988 - http::async_read with bind_front_handler breaks body_limit [Closed]

> Username: Spongman  
> Created at: 2025-02-22 08:53:49 UTC  
> Updated at: 2025-02-22 09:02:11 UTC  
> Closed at: 2025-02-22 09:02:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2988  

boost v1.77  
  
given  
```  
	boost::beast::tcp_stream stream_;  
	beast::flat_buffer buffer_;  
	http::request_parser<http::string_body> parser_;  
```  
  
if you call `http::async_read` and pass a lambda, like this:  
  
```  
http::async_read(  
	stream_,  
	buffer_,  
	parser_,  
	[self = shared_from_this()](beast::error_code ec, std::size_t size) { self->onRead(rc, size); }  
);  
```  
  
you get a callstack that looks like this:  
```  
boost::beast::http::detail::read_some_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy  
>, boost::beast::basic_flat_buffer<std::allocator<char> >, true>::read_some_op(boost::beast::http::detail::read_some_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true> * const this, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> & s, boost::beast::basic_flat_buffer<std::allocator<char> > & b, boost::beast::http::parser<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> > & p) (/usr/include/boost/beast/http/impl/read.hpp:169)  
boost::beast::http::async_read_some<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, void (boost::system::error_code, unsigned long)> >(boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>&, boost::beast::basic_flat_buffer<std::allocator<char> >&, boost::beast::http::basic_parser<true>&, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, void (boost::system::error_code, unsigned long)>&&)(boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> & stream, boost::beast::basic_flat_buffer<std::allocator<char> > & buffer, boost::beast::http::parser<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> > & parser, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void(boost::asio::any_io_executor)>, Session::start()::__lambda22, void(boost::system::error_code, long unsigned int)> && handler) (/usr/include/boost/beast/http/impl/read.hpp:461)  
boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>::operator()<boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, void (boost::system::error_code, unsigned long)> >(boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, void (boost::system::error_code, unsigned long)>&, boost::system::error_code, unsigned long)(boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done> * const this, boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void(boost::asio::any_io_executor)>, Session::start()::__lambda22, void(boost::system::error_code, long unsigned int)> & self, boost::beast::error_code ec, std::size_t bytes_transferred) (/usr/include/boost/beast/http/impl/read.hpp:300)  
boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, void (boost::system::error_code, unsigned long)>::operator()<>()(boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void(boost::asio::any_io_executor)>, Session::start()::__lambda22, void(boost::system::error_code, long unsigned int)> * const this) (/usr/include/boost/asio/impl/compose.hpp:354)  
boost::asio::detail::initiate_composed_op<void (boost::system::error_code, unsigned long), void (boost::asio::any_io_executor)>::operator()<Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done> >(Session::start()::{lambda(boost::system::error_code, unsigned long)#1}&&, boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>&&) const(const boost::asio::detail::initiate_composed_op<void(boost::system::error_code, long unsigned int), void(boost::asio::any_io_executor)> * const this, __lambda22 && handler, boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done> && impl) (/usr/include/boost/asio/impl/compose.hpp:513)  
_ZN5boost4asio6detail31completion_handler_async_resultIZN7Session5startEvEUlNS_6system10error_codeEmE_IFvS5_mEEE8initiateINS1_20initiate_composed_opIS7_FvNS0_15any_io_executorEEEES6_INS_5beast4http6detail7read_opINSE_12basic_streamINS0_2ip3tcpESB_NSE_21unlimited_rate_policyEEENSE_17basic_flat_bufferISaIcEEELb1ENSG_14parser_is_doneEEEEEEvOT_OT0_DpOT1_(boost::asio::detail::initiate_composed_op<void(boost::system::error_code, long unsigned int), void(boost::asio::any_io_executor)> && initiation, __lambda22 && token) (/usr/include/boost/asio/async_result.hpp:476)  
boost::asio::async_initiate<Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, void (boost::system::error_code, unsigned long), boost::asio::detail::initiate_composed_op<void (boost::system::error_code, unsigned long), void (boost::asio::any_io_executor)>, boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done> >(boost::asio::detail::initiate_composed_op<void (boost::system::error_code, unsigned long), void (boost::asio::any_io_executor)>, Session::start()::{lambda(boost::system::error_code, unsigned long)#1}&, boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>)(boost::asio::detail::initiate_composed_op<void(boost::system::error_code, long unsigned int), void(boost::asio::any_io_executor)> && initiation, __lambda22 & token) (/usr/include/boost/asio/async_result.hpp:858)  
boost::asio::async_compose<Session::start()::{lambda(boost::system::error_code, unsigned long)#1}, void (boost::system::error_code, unsigned long), boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>&>(boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done>&&, Session::start()::{lambda(boost::system::error_code, unsigned long)#1}&, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>&)(boost::beast::http::detail::read_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::detail::parser_is_done> && implementation, __lambda22 & token) (/usr/include/boost/asio/impl/compose.hpp:589)  
  
boost::beast::http::async_read<  
	boost::beast::basic_stream<  
		boost::asio::ip::tcp,  
		boost::asio::any_io_executor,  
		boost::beast::unlimited_rate_policy  
	>,  
	boost::beast::basic_flat_buffer<  
		std::allocator<char>   
	>,  
	true,  
	Session::start()::{lambda(boost::system::error_code,unsigned long)#1}  
>(  
	boost::beast::basic_stream<  
		boost::asio::ip::tcp,  
		boost::asio::any_io_executor,  
		boost::beast::unlimited_rate_policy  
	>&,  
	boost::beast::basic_flat_buffer<  
		std::allocator<char>   
	>&,  
	boost::beast::http::basic_parser<  
		true  
	>&,  
	Session::start()::{lambda(boost::system::error_code, unsigned long)#1}&&  
)(  
	boost::beast::basic_stream<  
		boost::asio::ip::tcp,  
		boost::asio::any_io_executor,  
		boost::beast::unlimited_rate_policy  
	> & stream,  
	boost::beast::basic_flat_buffer<  
		std::allocator<char>   
	> & buffer,  
	boost::beast::http::parser<  
		true,  
		boost::beast::http::basic_string_body<  
			char,  
			std::char_traits<char>,  
			std::allocator<char>   
		>,  
		std::allocator<char>   
	> & parser,  
__lambda22 && handler  
) (/usr/include/boost/beast/http/impl/read.hpp:615)  
```  
  
where `read_some_op::read_some_op()` takes a reference to the `basic_parser`. lovely.  
  
  
_however_, if you call it like this:  
```  
http::async_read(  
	stream_,  
	buffer_,  
	parser_,  
	bind_front_handler(&Session::onRead, shared_from_this())  
);  
```  
  
then you get a callstack that looks like this:  
```  
boost::beast::http::basic_parser<true>::basic_parser(boost::beast::http::basic_parser<true> * const this) (/usr/include/boost/beast/http/basic_parser.hpp:137)  
boost::beast::http::parser<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> >::parser<boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >, , void>(boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >&&)(boost::beast::http::parser<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> > * const this, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > > && arg1) (/usr/include/boost/beast/http/impl/parser.hpp:34)  
boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::data::data(boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::data * const this, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> & s_, boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::message_type & m_) (/usr/include/boost/beast/http/impl/read.hpp:80)  
boost::beast::detail::allocate_stable_state<boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::data, std::allocator<void> >::allocate_stable_state<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>&, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >&>(boost::beast::detail::allocate_stable_state<boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char>, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::data, std::allocator<void> > * const this, const std::allocator<void> & alloc) (/usr/include/boost/beast/core/impl/async_base.hpp:34)  
boost::beast::allocate_stable<boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::data, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> >, boost::asio::any_io_executor, std::allocator<void>, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>&, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >&>(boost::beast::stable_async_base<boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> >, boost::asio::any_io_executor, std::allocator<void> > & base) (/usr/include/boost/beast/core/impl/async_base.hpp:145)  
boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::read_msg_op<boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >(boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > > * const this, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > && h, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> & s, boost::beast::basic_flat_buffer<std::allocator<char> > & b, boost::beast::http::detail::read_msg_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > >::message_type & m) (/usr/include/boost/beast/http/impl/read.hpp:97)  
boost::beast::http::detail::run_read_msg_op::operator()<boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> >, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>, boost::beast::basic_flat_buffer<std::allocator<char> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> >(boost::beast::http::detail::run_read_msg_op * const this, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > && h, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> * s, boost::beast::basic_flat_buffer<std::allocator<char> > * b, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > > * m) (/usr/include/boost/beast/http/impl/read.hpp:142)  
boost::asio::detail::completion_handler_async_result<boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> >, void(boost::system::error_code, unsigned long)>::initiate<boost::beast::http::detail::run_read_msg_op, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> >, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>*, boost::beast::basic_flat_buffer<std::allocator<char> >*, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >*>(boost::beast::http::detail::run_read_msg_op && initiation, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > && token) (/usr/include/boost/asio/async_result.hpp:476)  
boost::asio::async_initiate<boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> >, void(boost::system::error_code, unsigned long), boost::beast::http::detail::run_read_msg_op, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>*, boost::beast::basic_flat_buffer<std::allocator<char> >*, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >*>(boost::beast::http::detail::run_read_msg_op && initiation, boost::beast::detail::bind_front_wrapper<void (Session::*)(boost::system::error_code, unsigned long), std::shared_ptr<Session> > & token) (/usr/include/boost/asio/async_result.hpp:856)  
  
boost::beast::http::async_read<  
	boost::beast::basic_stream<  
		boost::asio::ip::tcp,  
		boost::asio::any_io_executor,  
		boost::beast::unlimited_rate_policy  
	>,  
	boost::beast::basic_flat_buffer<  
		std::allocator<char>  
	>,  
	true,  
	boost::beast::http::basic_string_body<  
		char,  
		std::char_traits<char>,  
		std::allocator<char>  
	>,  
	std::allocator<char>,  
	boost::beast::detail::bind_front_wrapper<  
		void (Session::*)(boost::system::error_code, unsigned long),  
		std::shared_ptr<Session>  
	>  
>(  
	boost::beast::basic_stream<  
		boost::asio::ip::tcp,  
		boost::asio::any_io_executor,  
		boost::beast::unlimited_rate_policy  
	> & stream,  
	boost::beast::basic_flat_buffer<  
		std::allocator<char>  
	> & buffer,  
	boost::beast::http::message<  
		true,  
		boost::beast::http::basic_string_body<  
			char,  
			std::char_traits<char>,  
			std::allocator<char>   
		>,  
		boost::beast::http::basic_fields<  
			std::allocator<char>  
		>   
	> & msg,  
	boost::beast::detail::bind_front_wrapper<  
		void (Session::*)(boost::system::error_code, unsigned long),  
		std::shared_ptr<Session>   
	> && handler) (/usr/include/boost/beast/http/impl/read.hpp:703)  
```  
  
where, as you can see, `read_msg_op::read_msg_op()` constructs a NEW `basic_parser`, and _that_ parser has the default value for body_limit, and it's _that_ parser whose body_limit value is checked/decremented, and the limit set in the original parser is ignored.
