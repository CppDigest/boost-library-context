# #942 - program crash randomly on async write data [Closed]

> Username: liuaifu  
> Created at: 2017-12-20 03:20:05 UTC  
> Updated at: 2017-12-21 01:52:07 UTC  
> Closed at: 2017-12-21 01:52:07 UTC  
> Url: https://github.com/boostorg/beast/issues/942  

### Version of Beast  
  
boost1.66.0 beta  
  
### Steps necessary to reproduce the problem  
  
Crash randomly.  
  
### All relevant compiler information  
gcc6.3  
  
### stack  
```#0  0x00000000006890ae in raise ()  
#1  0x00000000006892aa in abort ()  
#2  0x000000000067c0c8 in __assert_fail_base ()  
#3  0x000000000067c12e in __assert_fail ()  
#4  0x000000000045ea44 in void boost::beast::websocket::detail::pausation::emplace<boost::beast::websocket::detail::pausation::saved_op<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::write_some_op<boost::asio::const_buffers_1, boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> > > > >(boost::beast::websocket::detail::pausation::saved_op<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::write_some_op<boost::asio::const_buffers_1, boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> > > >&&) ()  
#5  0x0000000000452665 in void boost::beast::websocket::detail::pausation::save<boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::write_some_op<boost::asio::const_buffers_1, boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> > > >(boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::write_some_op<boost::asio::const_buffers_1, boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> > >&&) ()  
#6  0x0000000000444625 in boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::write_some_op<boost::asio::const_buffers_1, boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> > >::operator()(boost::system::error_code, unsigned long, bool) ()  
#7  0x000000000043a79f in boost::asio::async_result<std::decay<boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> > >::type, void ()(boost::system::error_code, unsigned long)>::return_type boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >::async_write<boost::asio::const_buffers_1, boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> > >(boost::asio::const_buffers_1 const&, boost::asio::executor_binder<boost::_bi::bind_t<void, boost::_mfi::mf2<void, Client, boost::system::error_code const&, unsigned long>, boost::_bi::list3<boost::_bi::value<boost::shared_ptr<Client> >, boost::arg<1>, boost::arg<2> > >, boost::asio::strand<boost::asio::io_context::executor_type> >&&) ()  
#8  0x000000000040328d in Client::async_send_to_client(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >) ()```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-20 03:30:03 UTC  
> Url: https://github.com/boostorg/beast/issues/942#issuecomment-352955098  

This assert indicates that you are breaking the preconditions of the websocket stream. You must be trying to issue two calls to read at the same time, or two calls to write. You can't do that, you have to wait for a call to complete before you send another one. See:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety

---

## Comment 2

> Username: liuaifu  
> Created at: 2017-12-20 08:01:46 UTC  
> Updated at: 2017-12-20 08:12:44 UTC  
> Url: https://github.com/boostorg/beast/issues/942#issuecomment-352992368  

There have three async_* call which wrapped by strand, and no sync read/write call.  
The ioc run in five threads.  
```  
ws_.async_accept(  
	boost::asio::bind_executor(  
		strand_,  
		std::bind(  
			&Client::on_accept,  
			shared_from_this(),  
			std::placeholders::_1)));  
  
//...  
  
ws_.async_read(  
	buffer_,  
	boost::asio::bind_executor(  
		strand_,  
		boost::bind(&Client::on_client_msg, shared_from_this(), boost::placeholders::_1, boost::placeholders::_2)));  
  
//...  
  
ws_.async_write(client_write_buffer.data(),   
		boost::asio::bind_executor(  
			strand_,  
			boost::bind(&Client::on_client_write, shared_from_this(), boost::placeholders::_1, boost::placeholders::_2)  
		)  
	);  
```  
  
Now, i decrease to 1 thread, can avoid the problem?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-20 17:57:03 UTC  
> Url: https://github.com/boostorg/beast/issues/942#issuecomment-353136581  

This is not an issue of concurrency, it is that you must wait for the first read to complete before issuing the second one. Please read this page carefully:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety

---

## Comment 4

> Username: liuaifu  
> Created at: 2017-12-21 01:52:07 UTC  
> Url: https://github.com/boostorg/beast/issues/942#issuecomment-353235479  

@vinniefalco Now i known your mean, thank you.
