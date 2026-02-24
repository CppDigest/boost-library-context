# #3034 - Compilation Error:  use of deleted function of std::pmr::polymorphic_allocator in basic_multy_buffer on Boost 1.89.0 [Closed]

> Username: MafiaLogiki  
> Created at: 2025-10-01 21:06:50 UTC  
> Updated at: 2025-10-03 18:41:13 UTC  
> Closed at: 2025-10-03 18:41:13 UTC  
> Url: https://github.com/boostorg/beast/issues/3034  

# Summary  
When trying to use http::async_read with a request object that uses std::pmr::polymorphic_allocator, the compilation fails with a "use of deleted function" error. The error points to the copy-assignment operator of std::pmr::polymorphic_allocator being called from within the boost::beast::basic_multi_buffer  
  
# Minimal Reproducible Example:  
```  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/http/impl/read.hpp>  
#include <boost/core/ignore_unused.hpp>  
#include <memory_resource>  
  
namespace pmr {  
  using allocator_type = std::pmr::polymorphic_allocator<>;  
  
  using multi_buffer = boost::beast::basic_multi_buffer<allocator_type>;  
  using dynamic_body = boost::beast::http::basic_dynamic_body<multi_buffer>;  
  
  using flat_buffer = boost::beast::basic_flat_buffer<allocator_type>;  
  
  using fields = boost::beast::http::basic_fields<allocator_type>;   
} // namespace pmr  
    
using body_type = pmr::dynamic_body;  
  
using request_type = boost::beast::http::request<body_type, pmr::fields>;  
using response_type = boost::beast::http::response<body_type, pmr::fields>;  
  
using response_callback_type = std::function<void(response_type)>;  
using application_handler = std::function<void(const request_type&, response_callback_type)>;  
  
using namespace boost::beast;  
using namespace boost::asio::ip;  
  
int main() {  
  
  boost::asio::io_context io;  
  
  std::pmr::polymorphic_allocator<> alloc = {};  
  
  request_type req{request_type::header_type(alloc)};  
  response_type res{response_type::header_type(alloc)};  
  
  tcp::socket sock(io);  
  pmr::flat_buffer buf(alloc);  
  
  buf.reserve(8192);  
  
  http::async_read(sock, buf, req,  
        [](error_code err, size_t bytes) {  
          boost::ignore_unused(bytes);  
          boost::ignore_unused(err);  
      });  
}  
  
```  
# Compilation Error  
```  
/usr/include/boost/beast/core/impl/multi_buffer.hpp:1076:17: error: overload resolution selected deleted operator '='  
 1076 |     this->get() = std::move(other.get());  
      |     ~~~~~~~~~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/beast/core/impl/multi_buffer.hpp:1106:9: note: in instantiation of member function 'boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>::move_assign' requested here  
 1106 |         move_assign(other, std::true_type{});  
      |         ^  
/usr/include/boost/beast/core/impl/multi_buffer.hpp:615:5: note: in instantiation of member function 'boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>::move_assign' requested here  
  615 |     move_assign(other, pocma{});  
      |     ^  
/usr/include/boost/core/empty_value.hpp:52:7: note: in instantiation of member function 'boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>::operator=' requested here  
   52 | class empty_value {  
      |       ^  
/usr/include/boost/asio/composed.hpp:155:5: note: in instantiation of member function 'boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>>, true, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>>, std::pmr::polymorphic_allocator<>, (lambda at test.cpp:45:9)>::operator()' requested here  
  155 |     static_cast<Handler&&>(this->handler_)(static_cast<Args&&>(args)...);  
      |     ^  
/usr/include/boost/beast/http/impl/read.hpp:325:18: note: in instantiation of member function 'boost::asio::detail::composed_op<boost::beast::http::detail::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>>, true, boost::beast::http::detail::parser_is_done>, boost::asio::detail::composed_work<void (boost::asio::any_io_executor)>, boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>>, true, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>>, std::pmr::polymorphic_allocator<>, (lambda at test.cpp:45:9)>, void (boost::system::error_code, unsigned long)>::complete' requested here  
  325 |             self.complete(ec, bytes_transferred_);  
      |                  ^  
/usr/include/boost/asio/composed.hpp:149:5: note: (skipping 5 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
  149 |     this->impl_(*this, static_cast<T&&>(t)...);  
      |     ^  
/usr/include/boost/beast/http/impl/read.hpp:624:17: note: in instantiation of function template specialization 'boost::asio::async_compose<boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>>, true, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>>, std::pmr::polymorphic_allocator<>, (lambda at test.cpp:45:9)>, void (boost::system::error_code, unsigned long), boost::beast::http::detail::read_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>>, true, boost::beast::http::detail::parser_is_done>, boost::asio::basic_stream_socket<boost::asio::ip::tcp> &>' requested here  
  624 |     return net::async_compose<  
      |                 ^  
/usr/include/boost/beast/http/impl/read.hpp:152:9: note: in instantiation of function template specialization 'boost::beast::http::detail::read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>>, true, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>>, std::pmr::polymorphic_allocator<>, (lambda at test.cpp:45:9)>::read_msg_op<(lambda at test.cpp:45:9)>' requested here  
  152 |         read_msg_op<  
      |         ^  
/usr/include/boost/asio/async_result.hpp:329:5: note: in instantiation of function template specialization 'boost::beast::http::detail::run_read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>>::operator()<(lambda at test.cpp:45:9), boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>>, true, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>>, std::pmr::polymorphic_allocator<>>' requested here  
  329 |     static_cast<Initiation&&>(initiation)(  
      |     ^  
/usr/include/boost/asio/async_result.hpp:649:65: note: in instantiation of function template specialization 'boost::asio::detail::completion_handler_async_result<(lambda at test.cpp:45:9), void (boost::system::error_code, unsigned long)>::initiate<boost::beast::http::detail::run_read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>>, (lambda at test.cpp:45:9), boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>> *, boost::beast::http::message<true, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>>, boost::beast::http::basic_fields<std::pmr::polymorphic_allocator<>>> *>' requested here  
  649 |   return async_result<decay_t<CompletionToken>, Signatures...>::initiate(  
      |                                                                 ^  
/usr/include/boost/beast/http/impl/read.hpp:719:17: note: in instantiation of function template specialization 'boost::asio::async_initiate<(lambda at test.cpp:45:9), void (boost::system::error_code, unsigned long), boost::beast::http::detail::run_read_msg_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>>, boost::beast::basic_flat_buffer<std::pmr::polymorphic_allocator<>> *, boost::beast::http::message<true, boost::beast::http::basic_dynamic_body<boost::beast::basic_multi_buffer<std::pmr::polymorphic_allocator<>>>, boost::beast::http::basic_fields<std::pmr::polymorphic_allocator<>>> *>' requested here  
  719 |     return net::async_initiate<  
      |                 ^  
/usr/bin/../lib64/gcc/x86_64-suse-linux/15/../../../../include/c++/15/bits/memory_resource.h:155:7: note: candidate function has been explicitly deleted  
  155 |       operator=(const polymorphic_allocator&) = delete;  
      |       ^  
1 error generated.  
```  
  
# Environment  
- Boost version: 1.89.0  
- Compiler: Clang20  
- OS: openSUSE Tumbleweed

---

## Comment 1

> Username: ashtum  
> Created at: 2025-10-02 08:19:26 UTC  
> Updated at: 2025-10-02 08:31:03 UTC  
> Url: https://github.com/boostorg/beast/issues/3034#issuecomment-3359802454  

Thank you for reporting this and for providing a minimal reproducible example.  
I can confirm the issue, we attempt to move-assign when `pocma == false` and the allocators compares equal, which is correct. However, we are mistakenly using the same function as when `pocma == true`, which also tries to move-assign the allocators.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-10-02 12:24:08 UTC  
> Updated at: 2025-10-02 12:24:26 UTC  
> Url: https://github.com/boostorg/beast/issues/3034#issuecomment-3360962594  

This problem is neatly solved in Boost.Http.Proto. We simply don't use allocators: https://github.com/cppalliance/http_proto
