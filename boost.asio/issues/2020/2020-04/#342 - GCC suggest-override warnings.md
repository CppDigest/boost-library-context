# #342 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 18:56:23 UTC  
> Updated at: 2020-12-30 01:14:07 UTC  
> Closed at: 2020-12-30 01:14:06 UTC  
> Url: https://github.com/boostorg/asio/issues/342  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`. Duplicated warnings from same location are omitted:  
  
<pre>  
./boost/asio/detail/epoll_reactor.hpp:92:24: warning: ‘virtual void boost::asio::detail::epoll_reactor::shutdown()’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/epoll_reactor.hpp:95:24: warning: ‘virtual void boost::asio::detail::epoll_reactor::notify_fork(boost::asio::execution_context::fork_event)’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/impl/scheduler.ipp:321:3: warning: potential null pointer dereference [-Wnull-dereference]  
./boost/asio/detail/impl/scheduler.ipp:321:45: warning: potential null pointer dereference [-Wnull-dereference]  
./boost/asio/detail/posix_thread.hpp:84:18: warning: ‘void boost::asio::detail::posix_thread::func<Function>::run() [with Function = boost::asio::detail::resolver_service_base::work_scheduler_runner]’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/posix_thread.hpp:84:18: warning: ‘void boost::asio::detail::posix_thread::func<Function>::run() [with Function = boost::asio::detail::scheduler::thread_function]’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/posix_thread.hpp:84:18: warning: ‘void boost::asio::detail::posix_thread::func<Function>::run() [with Function = boost::asio::system_context::thread_function]’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/reactive_socket_service.hpp:85:8: warning: ‘void boost::asio::detail::reactive_socket_service<Protocol>::shutdown() [with Protocol = boost::asio::ip::udp]’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/resolver_service.hpp:63:8: warning: ‘void boost::asio::detail::resolver_service<Protocol>::shutdown() [with Protocol = boost::asio::ip::udp]’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/resolver_service.hpp:69:8: warning: ‘void boost::asio::detail::resolver_service<Protocol>::notify_fork(boost::asio::execution_context::fork_event) [with Protocol = boost::asio::ip::udp]’ can be marked override [-Wsuggest-override]  
./boost/asio/detail/scheduler.hpp:55:24: warning: ‘virtual void boost::asio::detail::scheduler::shutdown()’ can be marked override [-Wsuggest-override]  
./boost/asio/executor.hpp:39:39: warning: ‘virtual const char* boost::asio::bad_executor::what() const’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/error.ipp:102:15: warning: ‘virtual std::__cxx11::string boost::asio::error::detail::misc_category::message(int) const’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/error.ipp:35:15: warning: ‘virtual const char* boost::asio::error::detail::netdb_category::name() const’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/error.ipp:40:15: warning: ‘virtual std::__cxx11::string boost::asio::error::detail::netdb_category::message(int) const’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/error.ipp:67:15: warning: ‘virtual const char* boost::asio::error::detail::addrinfo_category::name() const’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/error.ipp:72:15: warning: ‘virtual std::__cxx11::string boost::asio::error::detail::addrinfo_category::message(int) const’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/error.ipp:97:15: warning: ‘virtual const char* boost::asio::error::detail::misc_category::name() const’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:150:14: warning: ‘boost::asio::executor::impl_base* boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::clone() const [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:156:8: warning: ‘void boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::destroy() [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:167:8: warning: ‘void boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::on_work_started() [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:172:8: warning: ‘void boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::on_work_finished() [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:177:22: warning: ‘boost::asio::execution_context& boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::context() [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:182:8: warning: ‘void boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::dispatch(boost::asio::executor::function&&) [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:187:8: warning: ‘void boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::post(boost::asio::executor::function&&) [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:192:8: warning: ‘void boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::defer(boost::asio::executor::function&&) [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:197:23: warning: ‘const std::type_info& boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::target_type() const [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>; boost::asio::executor::type_id_result_type = const std::type_info&]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:202:9: warning: ‘void* boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::target() [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:207:15: warning: ‘const void* boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::target() const [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:212:8: warning: ‘bool boost::asio::executor::impl< <template-parameter-1-1>, <template-parameter-1-2> >::equals(const boost::asio::executor::impl_base*) const [with Executor = boost::asio::io_context::executor_type; Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:267:14: warning: ‘boost::asio::executor::impl_base* boost::asio::executor::impl<boost::asio::system_executor, Allocator>::clone() const [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:272:8: warning: ‘void boost::asio::executor::impl<boost::asio::system_executor, Allocator>::destroy() [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:276:8: warning: ‘void boost::asio::executor::impl<boost::asio::system_executor, Allocator>::on_work_started() [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:281:8: warning: ‘void boost::asio::executor::impl<boost::asio::system_executor, Allocator>::on_work_finished() [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:286:22: warning: ‘boost::asio::execution_context& boost::asio::executor::impl<boost::asio::system_executor, Allocator>::context() [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:291:8: warning: ‘void boost::asio::executor::impl<boost::asio::system_executor, Allocator>::dispatch(boost::asio::executor::function&&) [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:296:8: warning: ‘void boost::asio::executor::impl<boost::asio::system_executor, Allocator>::post(boost::asio::executor::function&&) [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:301:8: warning: ‘void boost::asio::executor::impl<boost::asio::system_executor, Allocator>::defer(boost::asio::executor::function&&) [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:306:23: warning: ‘const std::type_info& boost::asio::executor::impl<boost::asio::system_executor, Allocator>::target_type() const [with Allocator = std::allocator<void>; boost::asio::executor::type_id_result_type = const std::type_info&]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:311:9: warning: ‘void* boost::asio::executor::impl<boost::asio::system_executor, Allocator>::target() [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:316:15: warning: ‘const void* boost::asio::executor::impl<boost::asio::system_executor, Allocator>::target() const [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/impl/executor.hpp:321:8: warning: ‘bool boost::asio::executor::impl<boost::asio::system_executor, Allocator>::equals(const boost::asio::executor::impl_base*) const [with Allocator = std::allocator<void>]’ can be marked override [-Wsuggest-override]  
./boost/asio/io_context.hpp:796:32: warning: ‘virtual void boost::asio::io_context::service::shutdown()’ can be marked override [-Wsuggest-override]  
./boost/asio/io_context.hpp:810:32: warning: ‘virtual void boost::asio::io_context::service::notify_fork(boost::asio::execution_context::fork_event)’ can be marked override [-Wsuggest-override]  
./boost/asio/ip/bad_address_cast.hpp:43:23: warning: ‘virtual const char* boost::asio::ip::bad_address_cast::what() const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-05-06 16:15:58 UTC  
> Url: https://github.com/boostorg/asio/issues/342#issuecomment-624745238  

See #347.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:14:05 UTC  
> Url: https://github.com/boostorg/asio/issues/342#issuecomment-752293622  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#759](https://github.com/chriskohlhoff/asio/issues/759).
