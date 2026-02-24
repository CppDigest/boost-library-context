# #2625 - boost::beast::http::request template error: Linker and Compiler not working [Closed]

> Username: WarrenN1  
> Created at: 2023-01-17 02:30:41 UTC  
> Updated at: 2023-10-28 16:34:19 UTC  
> Closed at: 2023-10-28 16:34:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2625  

My problem is as follows:  
The linker is struggling with boost::beast::http::request<Body, Allocator> when using template<Class Body, Class Allocator, Class Send> style functions. When I change the type to specifically match http::request\<http::string_body\> the compiler has a problem, but when I do the templating as shown below the linker has a problem. I am still new to beast so any help would be deeply appreciated.  
  
server.cpp  
```  
//--------------------------------------------------------------------------  
//Boost - Beast  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/beast/http/message.hpp>  
//--------------------------------------------------------------------------  
template<Class Body, Class Allocator, Class Send>  
awaitable<void> handle_request(asio::io_context& io_ctx,  
                                                         asio::thread_pool& tp_ctx,  
                                                         Cassandra_Ctx& cassandra_ctx,  
                                                         beast::string_view doc_root,  
                                                         http::request<Body, http::basic_fields<Allocator>>&& req,  
                                                         Send&& send)  
{  
...  
co_return co_await handle_login(std::ref(io_ctx), send, req, cass_future, std::string("test"), std::string("test"));  
...  
}  
```  
server.hpp  
```  
//--------------------------------------------------------------------------  
//Boost - Beast  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/beast/http/message.hpp>  
//--------------------------------------------------------------------------  
#include "req.handler.hpp"  
template<class Body, class Allocator, class Send>  
awaitable<void> handle_request(asio::io_context& io_ctx,  
                                                         asio::thread_pool& tp_ctx,  
                                                         Cassandra_Ctx& cassandra_ctx,  
                                                         beast::string_view doc_root,  
                                                         http::request<Body, http::basic_fields<Allocator>>&& req,  
                                                         Send&& send);  
```  
req_handler.hpp  
```  
//--------------------------------------------------------------------------  
//Boost - Beast  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/beast/http/message.hpp>  
//--------------------------------------------------------------------------  
...  
template<class Body, class Allocator, class Send>  
asio::awaitable<void> handle_login(asio::io_context& io_ctx,  
								   Send&& send,  
								   http::request<Body, http::basic_fields<Allocator>> req,  
								   CassFuture* result_future,  
								   const std::string email,  
								   const std::string password)  
...  
```  
  
They are compiled as such:  
```  
#Basically all linking etc. is done at this stage  
main : $(OBJS)  
	$(CC) $(CXXFLAGS) $(BUILD_OBJS) $(foreach inc,$(INC), -I$(inc)) $(foreach lib,$(LIB), -L$(lib)) $(FLAGS) -o build/$@  
  
Compiling server/server.o...  
g++ -std=c++2a -g -Wall -c project-webserver/server/server.cpp  -I/home/warrenniles/Desktop/project/project-webserver/external_libraries/include/  -I/home/warrenniles/Desktop/project/project-webserver/  -L/home/warrenniles/Desktop/project/project-webserver/external_libraries/lib/  -L/home/warrenniles/Desktop/project/project-webserver/ -lboost_coroutine -lboost_context -lboost_chrono -lboost_timer -lboost_container -lcrypto -lssl -lboost_system -lboost_url -lboost_json -pthread -fcoroutines -lcassandra -o build/project-webserver/server/server.o  
  
Compiling server/req_handler.o...  
g++ -std=c++2a -g -Wall -c project-webserver/server/req_handler.cpp  -I/home/warrenniles/Desktop/project/project-webserver/external_libraries/include/  -I/home/warrenniles/Desktop/project/project-webserver/  -L/home/warrenniles/Desktop/project/project-webserver/external_libraries/lib/  -L/home/warrenniles/Desktop/project/project-webserver/ -lboost_coroutine -lboost_context -lboost_chrono -lboost_timer -lboost_container -lcrypto -lssl -lboost_system -lboost_url -lboost_json -pthread -fcoroutines -lcassandra -o build/project-webserver/server/req_handler.o  
  
```  
  
The problem is that the linker keeps saying:  
```  
/opt/rh/gcc-toolset-11/root/usr/bin/ld: build/project-webserver/server/server.o: in function `handle_request(handle_request<boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, send_lambda&>(boost::asio::io_context&, boost::asio::thread_pool&, Cassandra_Ctx&, boost::core::basic_string_view<char>, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >&&, send_lambda&)::_Z14handle_requestIN5boost5beast4http17basic_string_bodyIcSt11char_traitsIcESaIcEEES6_R11send_lambdaENS0_4asio9awaitableIvNSA_15any_io_executorEEERNSA_10io_contextERNSA_11thread_poolER13Cassandra_CtxNS0_4core17basic_string_viewIcEEONS2_7messageILb1ET_NS2_12basic_fieldsIT0_EEEEOT1_.Frame*) [clone .actor]':  
/home/warrenniles/Desktop/project/project-webserver/project-webserver/server/server.cpp:305: undefined reference to `boost::asio::awaitable<void, boost::asio::any_io_executor> handle_login<boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char>, send_lambda&>(boost::asio::io_context&, send_lambda&, boost::beast::http::message<true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >, CassFuture_*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >)'  
collect2: error: ld returned 1 exit status  
```  
  
I don't know why this would be happening given that the function is in fact defined and included and both templates match. When I remove the req piece of it everything compiles as it should. I don't know why the linker would be having a problem so any advice would be deeply appreciated.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-17 02:57:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2625#issuecomment-1384772500  

This doesn't look like a beast issue, but like you having mismatched template argument lists for the declaration and definition of `handle_login`  
  
```  
undefined reference to `boost::asio::awaitable<void, boost::asio::any_io_executor> handle_login(...).  
```  
  
This is an undefined reference to your function, nothing in asio.

---

## Comment 2

> Username: WarrenN1  
> Created at: 2023-01-17 03:03:33 UTC  
> Updated at: 2023-01-17 03:04:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2625#issuecomment-1384775752  

@klemens-morgenstern the problem I'm having is that the templating should be working but it is not. It seems to be an issue with request and how it is interacting with the linked etc.  
  
I was hoping with some assistance as to understanding where the linker error is coming from.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-03-06 00:10:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2625#issuecomment-1455258315  

You can't put templates into source files.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-08-18 14:11:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2625#issuecomment-1683981410  

@WarrenN1 Is this still open?
