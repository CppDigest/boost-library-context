# #2048 - Soft mutex assertion fail in Websocket [Closed]

> Username: menkaur  
> Created at: 2020-08-12 01:43:21 UTC  
> Updated at: 2020-09-15 07:56:40 UTC  
> Closed at: 2020-09-15 07:56:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2048  

I've found this issue in  
BOOST_BEAST_VERSION 277  
  
I'm getting assertion fail in   
  
```  
template<class T>  
    void  
    unlock(T const*)  
    {  
        BOOST_ASSERT(id_ == T::id);  
        id_ = 0;  
    }  
  
```  
  
Stacktrace for the fail:  
  
```  
Exchanges.exe!boost::beast::websocket::detail::soft_mutex::unlock<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>(const boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1> * __formal) Line 75  
	at G:\boost\boost_1_72_0\boost\beast\websocket\detail\soft_mutex.hpp(75)  
Exchanges.exe!boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>::operator()(boost::system::error_code ec, unsigned __int64 bytes_transferred, bool cont) Line 428  
	at G:\boost\boost_1_72_0\boost\beast\websocket\impl\write.hpp(428)  
Exchanges.exe!boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>::operator()(const boost::system::error_code & ec, unsigned __int64 bytes_transferred, int start) Line 340  
	at G:\boost\boost_1_72_0\boost\asio\impl\write.hpp(340)  
Exchanges.exe!boost::beast::async_base<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>,boost::asio::executor,std::allocator<void>>::complete_now<boost::system::error_code &,unsigned __int64 &>(boost::system::error_code & <args_0>, unsigned __int64 & <args_1>) Line 382  
	at G:\boost\boost_1_72_0\boost\beast\core\async_base.hpp(382)  
Exchanges.exe!boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>::operator()(boost::system::error_code ec, unsigned __int64 bytes_transferred) Line 73  
	at G:\boost\boost_1_72_0\boost\beast\core\impl\flat_stream.hpp(73)  
Exchanges.exe!boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>::call_handler<boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>(boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>> & handler, const boost::system::error_code & ec, const unsigned __int64 & bytes_transferred) Line 56  
	at G:\boost\boost_1_72_0\boost\asio\ssl\detail\write_op.hpp(56)  
Exchanges.exe!boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>::operator()(boost::system::error_code ec, unsigned __int64 bytes_transferred, int start) Line 276  
	at G:\boost\boost_1_72_0\boost\asio\ssl\detail\io.hpp(276)  
Exchanges.exe!boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>::operator()(const boost::system::error_code & ec, unsigned __int64 bytes_transferred, int start) Line 340  
	at G:\boost\boost_1_72_0\boost\asio\impl\write.hpp(340)  
Exchanges.exe!boost::beast::async_base<boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>,boost::asio::executor,std::allocator<void>>::complete_now<boost::system::error_code &,unsigned __int64 &>(boost::system::error_code & <args_0>, unsigned __int64 & <args_1>) Line 382  
	at G:\boost\boost_1_72_0\boost\beast\core\async_base.hpp(382)  
Exchanges.exe!boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>>::operator()(boost::system::error_code ec, unsigned __int64 bytes_transferred) Line 381  
	at G:\boost\boost_1_72_0\boost\beast\core\impl\basic_stream.hpp(381)  
Exchanges.exe!boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>>,boost::system::error_code,unsigned __int64>::operator()() Line 166  
	at G:\boost\boost_1_72_0\boost\asio\detail\bind_handler.hpp(166)  
Exchanges.exe!boost::asio::detail::executor_function<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>>,boost::system::error_code,unsigned __int64>,std::allocator<void>>::do_complete(boost::asio::detail::executor_function_base * base, bool call) Line 91  
	at G:\boost\boost_1_72_0\boost\asio\detail\executor_function.hpp(91)  
Exchanges.exe!boost::asio::detail::executor_function_base::complete() Line 33  
	at G:\boost\boost_1_72_0\boost\asio\detail\executor_function.hpp(33)  
Exchanges.exe!boost::asio::executor::function::operator()() Line 71  
	at G:\boost\boost_1_72_0\boost\asio\impl\executor.hpp(71)  
Exchanges.exe!boost::asio::asio_handler_invoke<boost::asio::executor::function>(boost::asio::executor::function & function, ...) Line 70  
	at G:\boost\boost_1_72_0\boost\asio\handler_invoke_hook.hpp(70)  
Exchanges.exe!boost_asio_handler_invoke_helpers::invoke<boost::asio::executor::function,boost::asio::executor::function>(boost::asio::executor::function & function, boost::asio::executor::function & context) Line 39  
	at G:\boost\boost_1_72_0\boost\asio\detail\handler_invoke_helpers.hpp(39)  
Exchanges.exe!boost::asio::io_context::executor_type::dispatch<boost::asio::executor::function,std::allocator<void>>(boost::asio::executor::function && f, const std::allocator<void> & a) Line 265  
	at G:\boost\boost_1_72_0\boost\asio\impl\io_context.hpp(265)  
Exchanges.exe!boost::asio::executor::impl<boost::asio::io_context::executor_type,std::allocator<void>>::dispatch(boost::asio::executor::function && f) Line 185  
	at G:\boost\boost_1_72_0\boost\asio\impl\executor.hpp(185)  
Exchanges.exe!boost::asio::executor::dispatch<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>>,boost::system::error_code,unsigned __int64>,std::allocator<void>>(boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>>,boost::system::error_code,unsigned __int64> && f, const std::allocator<void> & a) Line 351  
	at G:\boost\boost_1_72_0\boost\asio\impl\executor.hpp(351)  
Exchanges.exe!??@82edbc8bc0e1ca2ce9e040e6eef19c10@(boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>>,boost::system::error_code,unsigned __int64> & function, boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>> & handler) Line 74  
	at G:\boost\boost_1_72_0\boost\asio\detail\handler_work.hpp(74)  
Exchanges.exe!boost::asio::detail::win_iocp_socket_send_op<boost::beast::buffers_prefix_view<boost::asio::const_buffers_1>,boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::ops::transfer_op<0,boost::asio::const_buffers_1,boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::mutable_buffer,boost::asio::mutable_buffer const *,boost::asio::detail::transfer_all_t,boost::asio::ssl::detail::io_op<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>,boost::asio::ssl::detail::write_op<boost::asio::mutable_buffer>,boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::ops::write_op<boost::asio::detail::write_op<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>,boost::beast::buffers_cat_view<boost::asio::mutable_buffer,boost::asio::mutable_buffers_1>::const_iterator,boost::asio::detail::transfer_all_t,boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>,1>::write_some_op<void <lambda>(boost::system::error_code, unsigned __int64),boost::asio::mutable_buffers_1>>>>>>,boost::asio::detail::io_object_executor<boost::asio::executor>>::do_complete(void * owner, boost::asio::detail::win_iocp_operation * base, const boost::system::error_code & result_ec, unsigned __int64 bytes_transferred) Line 96  
	at G:\boost\boost_1_72_0\boost\asio\detail\win_iocp_socket_send_op.hpp(96)  
Exchanges.exe!boost::asio::detail::win_iocp_operation::complete(void * owner, const boost::system::error_code & ec, unsigned __int64 bytes_transferred) Line 48  
	at G:\boost\boost_1_72_0\boost\asio\detail\win_iocp_operation.hpp(48)  
Exchanges.exe!boost::asio::detail::win_iocp_io_context::do_one(unsigned long msec, boost::system::error_code & ec) Line 460  
	at G:\boost\boost_1_72_0\boost\asio\detail\impl\win_iocp_io_context.ipp(460)  
Exchanges.exe!boost::asio::detail::win_iocp_io_context::run(boost::system::error_code & ec) Line 202  
	at G:\boost\boost_1_72_0\boost\asio\detail\impl\win_iocp_io_context.ipp(202)  
Exchanges.exe!boost::asio::io_context::run() Line 63  
	at G:\boost\boost_1_72_0\boost\asio\impl\io_context.ipp(63)  
  
```  
  
Looks like the thread unlocks the mutex, than it unlocks it again. The issue occurs randomly with low frequency  
  
I've checked some locals in   
```  
template<class NextLayer, bool deflateSupported>  
template<class Buffers, class Handler>  
void  
stream<NextLayer, deflateSupported>::  
write_some_op<Buffers, Handler>::  
operator()(  
```  
  
how_ =2  
impl.check_stop_now(ec) = false  
remain_=0  
bytes_transferred_=90  
n=14757395258967641292  
  
all of   
	if( impl.op_close.maybe_invoke()  
		|| impl.op_idle_ping.maybe_invoke()  
		|| impl.op_rd.maybe_invoke()  
		|| impl.op_ping.maybe_invoke())   
		  
are false  
  
looks like it acquired the lock, than did the cycle, went to `upcall` and found that the lock is not locked  
  
Can the issue be that in soft_mutex,   
```  
int id_ = 0;  
```  
is not atomic?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-12 06:21:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-672634049  

hi @menkaur, you mention atomic.  
  
Should I infer from this that your program is multi-threaded?  
  
If so you will need to guard concurrent access to mutable member functions of the websocket (and indeed any beast or asio objects).  
  
IO Objects are not thread-safe with respect to concurrent access that mutates state. This is by design as it means authors of single-threaded programs do not pay the cost of unnecessary locks.

---

## Comment 2

> Username: menkaur  
> Created at: 2020-08-12 12:43:19 UTC  
> Updated at: 2020-08-12 12:46:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-672847748  

@madmongo1 yes, the app is multithread. I'm running several sockets, each of them in full duplex mode. I keep track not to call 2 async writes or reads at the same time, and not to call initialization of write/read/close simultaneously.  
  
when does mutable state start and end? should I lock around every async call, or should I take measures for the locks to persist throughout the calls/after async callback finishes processing?  Is async callback always processed in separate thread?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-12 14:02:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-672890272  

Have you used Asio before?

---

## Comment 4

> Username: menkaur  
> Created at: 2020-08-12 14:22:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-672902244  

No  
  
On Wed, Aug 12, 2020 at 10:03 AM Vinnie Falco <notifications@github.com>  
wrote:  
  
> Have you used Asio before?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2048#issuecomment-672890272>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AABJ7WWDRDI34OUJFDGQSD3SAKOJ7ANCNFSM4P337OXQ>  
> .  
>

---

## Comment 5

> Username: menkaur  
> Created at: 2020-08-14 02:16:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-673845772  

I have surrounded with lock every call to local instance of:  
- ioc;		  
- ctx;  
- websocket;  
- resolver;  
- buffer;  
  
And I just got this bug again. There must be more to in than 2 threads modifying single instance at the same time

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-08-14 02:17:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-673846055  

You aren't supposed to use mutex with asio. How many threads does your program use?

---

## Comment 7

> Username: menkaur  
> Created at: 2020-08-14 02:28:29 UTC  
> Updated at: 2020-08-14 02:29:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-673848778  

I use 1 thread for every websocket I run. For uplink messages, I'm using queue, and I'm feeding next message in the callback method for the write success. Read callback feeds messages from server into processing queue. and immediately initiates next read From what I understand, both read and write callbacks are called from the .run thread.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-08-14 02:29:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-673849139  

Read this: https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/overview/core/strands.html

---

## Comment 9

> Username: menkaur  
> Created at: 2020-08-14 02:32:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-673849896  

Is there an issue with the way I implemented it?

---

## Comment 10

> Username: menkaur  
> Created at: 2020-08-14 02:41:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-673851970  

I'm going to rewrite the program with strands and will see how that works

---

## Comment 11

> Username: menkaur  
> Created at: 2020-08-14 03:39:41 UTC  
> Updated at: 2020-08-14 03:40:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-673865650  

I'm using strands like this:  
  
```  
ws.async_read(  
                w.buffer,   
                boost::asio::bind_executor(mystrand,myLambda));  
```  
  
The compiler complains about this:  
  
```  
G:\boost\boost_1_72_0\boost\asio\bind_executor.hpp(115): warning C4996: 'std::_Arg_types<boost::beast::error_code>::argument_type': warning STL4007: Many result_type typedefs and all argument_type, first_argument_type, and second_argument_type typedefs are deprecated in C++17. You can define _SILENCE_CXX17_ADAPTOR_TYPEDEFS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
```  
  
Is this correct way to use strands in c++17 and later?

---

## Comment 12

> Username: menkaur  
> Created at: 2020-08-16 15:23:12 UTC  
> Updated at: 2020-08-16 15:24:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-674539891  

I've put ioc.dispatch(...) around every call to any of the websocket members, yet the problem persists. Next I'm trying locking around the initialisation part of my code

---

## Comment 13

> Username: menkaur  
> Created at: 2020-08-16 15:25:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-674540157  

Is ioc.dispatch itself thread safe?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2020-08-16 15:26:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-674540274  

Yes `dispatch` is safe to call concurrently, but it sounds to me like you do not know how to use Asio in a thread-safe way.

---

## Comment 15

> Username: menkaur  
> Created at: 2020-08-16 16:30:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-674547725  

looks like it. any resources you can recommend?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2020-08-16 16:39:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-674548660  

I don't know of any. I learned by examining example programs and doing experiments, plus reading all the stuff there is on the web such as blogs and what not.

---

## Comment 17

> Username: madmongo1  
> Created at: 2020-09-15 07:56:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2048#issuecomment-692538193  

@menkaur you might get some mileage from joining the cpplang slack server and asking for help in the #beast channel.   
Be sure to have done _some_ reading prior.
