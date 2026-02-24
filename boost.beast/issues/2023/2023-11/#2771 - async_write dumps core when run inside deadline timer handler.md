# #2771 - async_write dumps core when run inside deadline timer handler [Closed]

> Username: paulbkeelp  
> Created at: 2023-11-22 08:10:56 UTC  
> Updated at: 2023-11-22 08:48:34 UTC  
> Closed at: 2023-11-22 08:48:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2771  

boost/beast version 71  
  
      
Sending the message from client to server based on timeinterval dumps during run time after running some time like few seconds   
  
/usr/include/boost/beast/websocket/detail/soft_mutex.hpp:89: bool boost::beast::websocket::detail::soft_mutex::try_lock(const T*)  
 [with T = boost::beast::websocket::stream<boost::beast::basic_stream  
<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream  
<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)  
(boost::system::error_code, long unsigned int), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>]:   
Assertion `id_ != T::id' failed.  
  
Aborted (core dumped)  
  
Sample example from async websocket client :  
    void  
    on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
        timer_.async_wait(boost::bind(&session::timefunction, shared_from_this(), boost::asio::placeholders::error));  
    }  
  
    void timefunction(const boost::system::error_code& ec)  
    {  
       ws_.async_write(  
            net::buffer("test"),  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
  
       timer_.expires_at(timer_.expires_at() + interval);  
       timer_.async_wait(boost::bind(&session::timefunction, shared_from_this(), boost::asio::placeholders::error));  
    }  
   
    void  
    on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
        if(ec)  
            return fail(ec, "write");  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
        if(ec)  
            return fail(ec, "read");  
    }  
  
  
  
construction  
 explicit  
    session(net::io_context& ioc)  
        : resolver_(net::make_strand(ioc))  
        , ws_(net::make_strand(ioc))  
        , timer_(net::make_strand(ioc), interval)  
    {  
    }  
  
boost::posix_time::milliseconds interval(1);  
  
newasync: /usr/include/boost/beast/websocket/detail/soft_mutex.hpp:89: bool boost::beast::websocket::detail::soft_mutex::try_lock(const T*) [with T = boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, long unsigned int), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>]: Assertion `id_ != T::id' failed.  
Aborted (core dumped)  
osboxes@osboxes:~/queue$   
  
----------------------------------------------------  
GDB  
  
newasync: /usr/include/boost/beast/websocket/detail/soft_mutex.hpp:89: bool boost::beast::websocket::detail::soft_mutex::try_lock(const T*) [with T = boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, long unsigned int), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>]: Assertion `id_ != T::id' failed.  
  
Thread 1 "newasync" received signal SIGABRT, Aborted.  
__GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:50  
50	../sysdeps/unix/sysv/linux/raise.c: No such file or directory.  
-------------------------------------------------------------------------------------------------------------------------------  
  
  
Thread 1 "newasync" received signal SIGABRT, Aborted.  
__GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:50  
50	../sysdeps/unix/sysv/linux/raise.c: No such file or directory.  
(gdb) where  
#0  __GI_raise (sig=sig@entry=6) at ../sysdeps/unix/sysv/linux/raise.c:50  
#1  0x00007ffff7bc1859 in __GI_abort () at abort.c:79  
#2  0x00007ffff7bc1729 in __assert_fail_base (fmt=0x7ffff7d57588 "%s%s%s:%u: %s%sAssertion `%s' failed.\n%n", assertion=0x555555679c99 "id_ != T::id",   
    file=0x555555679c60 "/usr/include/boost/beast/websocket/detail/soft_mutex.hpp", line=89, function=<optimised out>) at assert.c:92  
#3  0x00007ffff7bd2fd6 in __GI___assert_fail (assertion=0x555555679c99 "id_ != T::id", file=0x555555679c60 "/usr/include/boost/beast/websocket/detail/soft_mutex.hpp",   
    line=89,   
    function=0x55555567b2d0 "bool boost::beast::websocket::detail::soft_mutex::try_lock(const T*) [with T = boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unl"...) at assert.c:101  
#4  0x00005555555c406c in boost::beast::websocket::detail::soft_mutex::try_lock<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer> > (this=0x555555746354)  
    at /usr/include/boost/beast/websocket/detail/soft_mutex.hpp:89  
#5  0x00005555555be0c9 in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>::operator() (this=0x7fffffffc1d0, ec=..., bytes_transferred=0, cont=false) at /usr/include/boost/beast/websocket/impl/read.hpp:101  
#6  0x00005555555b93d4 in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast--Type <RET> for more, q to quit, c to continue without paging--  
::basic_flat_buffer<std::allocator<char> > > > (this=0x7fffffffc1d0, h=..., sp=..., bs=...) at /usr/include/boost/beast/websocket/impl/read.hpp:78  
#7  0x00005555555b3af9 in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >::operator() (this=0x7fffffffc3a0, ec=..., bytes_transferred=0, cont=false) at /usr/include/boost/beast/websocket/impl/read.hpp:679  
#8  0x00005555555ada5e in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > >::read_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> > > (  
    this=0x7fffffffc3a0, h=..., sp=..., b=..., limit=0, some=false) at /usr/include/boost/beast/websocket/impl/read.hpp:648  
#9  0x00005555555a9378 in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::run_read_op::operator()<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::beast::basic_flat_buffer<std::allocator<char> > > (this=0x7fffffffc4ea, h=..., sp=..., b=0x555555745058, limit=0, some=false) at /usr/include/boost/beast/websocket/impl/read.hpp:751  
#10 0x00005555555a3e27 in boost::asio::async_result<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, void (boost::system::error_code, unsigned long)>::initiate<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::run_read_op, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::shared_ptr<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::impl_type>&, boost::beast::basic_flat_buffer<std::allocator<char> >*, int, bool>(boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::run_read_op&&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >&&, boost::shared_ptr<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::impl_type>&, boost::beast::basic_flat_buffer<std::allocator<char> >*&&, int&&, bool&&) (initiation=..., token=...)  
    at /usr/include/boost/asio/async_result.hpp:82  
#11 0x000055555559be37 in boost::asio::async_initiate<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, void (boost::system::error_code, unsigned long), boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast:--Type <RET> for more, q to quit, c to continue without paging--   
:unlimited_rate_policy>, true>::run_read_op, boost::shared_ptr<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::impl_type>&, boost::beast::basic_flat_buffer<std::allocator<char> >*, int, bool>(boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::run_read_op&&, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >&, boost::shared_ptr<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::impl_type>&, boost::beast::basic_flat_buffer<std::allocator<char> >*&&, int&&, bool&&) (initiation=..., token=...)  
    at /usr/include/boost/asio/async_result.hpp:257  
#12 0x0000555555592f12 in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::async_read<boost::beast::basic_flat_buffer<std::allocator<char> >, boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> > > (this=0x555555745048, buffer=..., handler=...) at /usr/include/boost/beast/websocket/impl/read.hpp:817  
#13 0x000055555558a7b9 in session::on_write (this=0x555555745010, ec=..., bytes_transferred=5) at newasync.cpp:172  
#14 0x00005555555d0d30 in std::__invoke_impl<void, void (session::* const&)(boost::system::error_code, unsigned long), std::shared_ptr<session>, boost::system::error_code&, unsigned long&> (__f=  
    @0x7fffffffc6c0: (void (session::*)(class session * const, class boost::system::error_code, unsigned long)) 0x55555558a6b8 <session::on_write(boost::system::error_code, unsigned long)>, __t=...) at /usr/include/c++/9/bits/invoke.h:73  
#15 0x00005555555cc1a4 in std::__invoke<void (session::* const&)(boost::system::error_code, unsigned long), std::shared_ptr<session>, boost::system::error_code&, unsigned long&> (__fn=  
    @0x7fffffffc6c0: (void (session::*)(class session * const, class boost::system::error_code, unsigned long)) 0x55555558a6b8 <session::on_write(boost::system::error_code, unsigned long)>) at /usr/include/c++/9/bits/invoke.h:95  
#16 0x00005555555c7be2 in std::_Mem_fn_base<void (session::*)(boost::system::error_code, unsigned long), true>::operator()<std::shared_ptr<session>, boost::system::error_code&, unsigned long&> (this=0x7fffffffc6c0) at /usr/include/c++/9/functional:114  
#17 0x00005555555c3345 in boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >::invoke<0ul, boost::system::error_code&, unsigned long&> (this=0x7fffffffcc70) at /usr/include/boost/beast/core/detail/bind_handler.hpp:235  
--Type <RET> for more, q to quit, c to continue without paging--  
#18 0x00005555555bcd70 in boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >::operator()<boost::system::error_code&, unsigned long&> (this=0x7fffffffcc70) at /usr/include/boost/beast/core/detail/bind_handler.hpp:258  
#19 0x00005555555b7e69 in boost::beast::async_base<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::executor, std::allocator<void> >::complete<boost::system::error_code&, unsigned long&> (this=0x7fffffffcc68, is_continuation=true)  
    at /usr/include/boost/beast/core/async_base.hpp:353  
#20 0x00005555555b36c6 in boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1>::operator() (this=0x7fffffffcc68, ec=..., bytes_transferred=11, cont=true) at /usr/include/boost/beast/websocket/impl/write.hpp:432  
#21 0x00005555555d195c in boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> >::operator() (this=0x7fffffffcc18, ec=..., bytes_transferred=11, start=0) at /usr/include/boost/asio/impl/write.hpp:338  
#22 0x00005555555f31ac in boost::beast::async_base<boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> >, boost::asio::executor, std::allocator<void> >::complete_now<boost::system::error_code&, unsigned long&> (  
    this=0x7fffffffcc10) at /usr/include/boost/beast/core/async_base.hpp:377  
#23 0x00005555555ed66f in boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, --Type <RET> for more, q to quit, c to continue without paging--  
boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >::operator() (this=0x7fffffffcc10, ec=..., bytes_transferred=11)  
    at /usr/include/boost/beast/core/impl/basic_stream.hpp:377  
#24 0x0000555555627f09 in boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >, boost::system::error_code, unsigned long>::operator() (this=0x7fffffffcc10)  
    at /usr/include/boost/asio/detail/bind_handler.hpp:164  
#25 0x000055555562349a in boost::asio::detail::executor_function<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >, boost::system::error_code, unsigned long>, std::allocator<void> >::do_complete (base=0x555555749eb0, call=true) at /usr/include/boost/asio/detail/executor_function.hpp:91  
#26 0x000055555557056d in boost::asio::detail::executor_function_base::complete (this=0x555555749eb0) at /usr/include/boost/asio/detail/executor_function.hpp:32  
#27 0x000055555557065e in boost::asio::executor::function::operator() (this=0x7fffffffd000) at /usr/include/boost/asio/impl/executor.hpp:69  
#28 0x000055555566fcd4 in boost::asio::asio_handler_invoke<boost::asio::executor::function> (function=...) at /usr/include/boost/asio/handler_invoke_hook.hpp:69  
#29 0x000055555566f4a3 in boost_asio_handler_invoke_helpers::invoke<boost::asio::executor::function, boost::asio::executor::function> (function=..., context=...)  
--Type <RET> for more, q to quit, c to continue without paging--  
    at /usr/include/boost/asio/detail/handler_invoke_helpers.hpp:37  
#30 0x000055555566ff28 in boost::asio::detail::executor_op<boost::asio::executor::function, std::allocator<void>, boost::asio::detail::scheduler_operation>::do_complete (  
    owner=0x555555745a00, base=0x55555574a130) at /usr/include/boost/asio/detail/executor_op.hpp:70  
#31 0x00005555555681ba in boost::asio::detail::scheduler_operation::complete (this=0x55555574a130, owner=0x555555745a00, ec=..., bytes_transferred=0)  
    at /usr/include/boost/asio/detail/scheduler_operation.hpp:40  
#32 0x00005555556706fb in boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>::operator() (this=0x7fffffffd210)  
    at /usr/include/boost/asio/detail/impl/strand_executor_service.hpp:90  
#33 0x000055555567039e in boost::asio::asio_handler_invoke<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const> > (  
    function=...) at /usr/include/boost/asio/handler_invoke_hook.hpp:69  
#34 0x0000555555670060 in boost_asio_handler_invoke_helpers::invoke<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>, boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const> > (function=..., context=...)  
    at /usr/include/boost/asio/detail/handler_invoke_helpers.hpp:37  
#35 0x000055555566f65a in boost::asio::io_context::executor_type::dispatch<boost::asio::detail::strand_executor_service::invoker<boost::asio::io_context::executor_type const>, std::allocator<void> > (this=0x5555557465e8, f=..., a=...) at /usr/include/boost/asio/impl/io_context.hpp:265  
#36 0x000055555566e644 in boost::asio::detail::strand_executor_service::dispatch<boost::asio::io_context::executor_type const, boost::asio::executor::function, std::allocator<void> > (impl=std::shared_ptr<class boost::asio::detail::strand_executor_service::strand_impl> (use count 2, weak count 0) = {...}, ex=..., function=..., a=...)  
    at /usr/include/boost/asio/detail/impl/strand_executor_service.hpp:128  
#37 0x000055555566ca80 in boost::asio::strand<boost::asio::io_context::executor_type>::dispatch<boost::asio::executor::function, std::allocator<void> > (this=0x5555557465e8,   
    f=..., a=...) at /usr/include/boost/asio/strand.hpp:193  
#38 0x000055555566ae79 in boost::asio::executor::impl<boost::asio::strand<boost::asio::io_context::executor_type>, std::allocator<void> >::dispatch (this=0x5555557465d0,   
    f=...) at /usr/include/boost/asio/impl/executor.hpp:184  
#39 0x0000555555614f50 in boost::asio::executor::dispatch<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::un--Type <RET> for more, q to quit, c to continue without paging--  
limited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >, boost::system::error_code, unsigned long>, std::allocator<void> > (this=0x7fffffffd6b0, f=..., a=...) at /usr/include/boost/asio/impl/executor.hpp:351  
#40 0x000055555560fa27 in boost::asio::detail::handler_work<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >, boost::asio::detail::io_object_executor<boost::asio::executor>, boost::asio::executor>::complete<boost::asio::detail::binder2<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >, boost::system::error_code, unsigned long> > (this=0x7fffffffd6a0, function=...,   
    handler=warning: RTTI symbol not found for class 'boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >'  
...) at /usr/include/boost/asio/detail/handler_work.hpp:72  
#41 0x000055555560ac9e in boost::asio::detail::reactive_socket_send_op<boost::beast::buffers_prefix_view<boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul> >, boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>::ops::transfer_op<false, boost::asio::detail::prepared_buffers<boost::asio::const_buffer, 64ul>, boost::asio::detail::write_op<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, --Type <RET> for more, q to quit, c to continue without paging--  
boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>, boost::beast::buffers_cat_view<boost::asio::mutable_buffer, boost::asio::mutable_buffers_1>::const_iterator, boost::asio::detail::transfer_all_t, boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>, true>::write_some_op<boost::beast::detail::bind_front_wrapper<void (session::*)(boost::system::error_code, unsigned long), std::shared_ptr<session> >, boost::asio::const_buffers_1> > >, boost::asio::detail::io_object_executor<boost::asio::executor> >::do_complete (owner=0x555555744f00, base=0x55555574a130)  
    at /usr/include/boost/asio/detail/reactive_socket_send_op.hpp:122  
#42 0x00005555555681ba in boost::asio::detail::scheduler_operation::complete (this=0x55555574a130, owner=0x555555744f00, ec=..., bytes_transferred=0)  
    at /usr/include/boost/asio/detail/scheduler_operation.hpp:40  
#43 0x000055555556d494 in boost::asio::detail::scheduler::do_run_one (this=0x555555744f00, lock=..., this_thread=..., ec=...)  
    at /usr/include/boost/asio/detail/impl/scheduler.ipp:447  
#44 0x000055555556cd50 in boost::asio::detail::scheduler::run (this=0x555555744f00, ec=...) at /usr/include/boost/asio/detail/impl/scheduler.ipp:200  
#45 0x000055555557025e in boost::asio::io_context::run (this=0x7fffffffdb20) at /usr/include/boost/asio/impl/io_context.ipp:63  
#46 0x000055555556579c in main (argc=4, argv=0x7fffffffdc58) at newasync.cpp:235  
(gdb)   
(gdb)

---

## Comment 1

> Username: paulbkeelp  
> Created at: 2023-11-22 08:48:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2771#issuecomment-1822339685  

seems to be sync. issue
