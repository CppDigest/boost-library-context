# #1134 - compile fiber yield problem [Closed]

> Username: jxfwinter  
> Created at: 2018-05-18 09:05:46 UTC  
> Updated at: 2018-05-19 05:33:38 UTC  
> Closed at: 2018-05-19 05:33:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1134  

beast integrate with boost::fibers problem  
  
I use boost::fibers::asio::yield  
https://www.boost.org/doc/libs/1_67_0/libs/fiber/examples/asio/yield.hpp  
  
https://www.boost.org/doc/libs/1_67_0/libs/fiber/examples/asio/detail/yield.hpp  
  
this is a test code  
  
```  
#include <string>  
#include <thread>  
#include <list>  
#include <map>  
#include <memory>  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/asio/ssl/error.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/fiber/all.hpp>  
  
using namespace boost::asio;  
using namespace boost::beast;  
  
#include "yield.hpp"  
  
void test_code() {  
  boost::system::error_code ec;  
  tcp::socket socket;  
  http::request<http::string_body> req{http::verb::get, "www.boost.org", 11};  
  http::async_write(socket, req, boost::fibers::asio::yield[ec]);  
)  
int main() {  
 return 0;  
}  
  
```  
centos g++ x64 7.2  
with boost 1.66, this code compile success,  
but with boost 1.67, this code compile failed,  
the error message  
  
/root/abc/src/tool/../../include/boost/asio/detail/bind_handler.hpp:150:24: error: use of deleted function ‘boost::beast::http::detail::write_some_op<Stream, Handler, isRequest, Body, Fields>::write_some_op(const boost::beast::http::detail::write_some_op<Stream, Handler, isRequest, Body, Fields>&) [with Stream = boost::asio::basic_stream_socket<boost::asio::ip::tcp>; Handler = boost::beast::http::detail::write_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::fibers::asio::detail::yield_handler<long unsigned int>, boost::beast::http::detail::serializer_is_done, false, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >; bool isRequest = false; Body = boost::beast::http::basic_string_body<char>; Fields = boost::beast::http::basic_fields<std::allocator<char> >]’  
       arg2_(other.arg2_)  
                        ^  
In file included from /root/abc/src/tool/../../include/boost/beast/http/write.hpp:567:0,  
                 from /root/abc/src/tool/../../include/boost/beast/http.hpp:36,  
                 from /root/abc/src/tool/../../include/tool/restful_api_server.h:11,  
                 from /root/abc/src/tool/restful_api_server.cpp:7:  
/root/abc/src/tool/../../include/boost/beast/http/impl/write.ipp:71:5: note: declared here  
     write_some_op(write_some_op const&) = delete;

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-18 12:57:45 UTC  
> Updated at: 2018-05-18 13:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1134#issuecomment-390198685  

It looks like `yield_handler` here does not support move construction:  
https://github.com/boostorg/fiber/blob/develop/examples/asio/detail/yield.hpp#L141

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-05-18 14:30:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1134#issuecomment-390225578  

I am working on a fix...
