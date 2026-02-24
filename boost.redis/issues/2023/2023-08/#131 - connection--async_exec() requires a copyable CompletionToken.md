# #131 - connection::async_exec() requires a copyable CompletionToken [Closed]

> Username: cbodley  
> Created at: 2023-08-02 15:45:27 UTC  
> Updated at: 2023-08-02 20:06:28 UTC  
> Closed at: 2023-08-02 20:06:28 UTC  
> Url: https://github.com/boostorg/redis/issues/131  

with gcc 13.1.1, boost 1.82, and redis develop branch at commit 7d162597496becdca3c72820b3c40c84a85debf9:  
```c++  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
  
struct move_only_handler {  
  move_only_handler(const move_only_handler&) = delete;  
  move_only_handler(move_only_handler&&) = default;  
  
  void operator()(boost::system::error_code, std::size_t);  
};  
  
int main(int argc, char** argv)  
{  
  boost::asio::io_context ioctx;  
  boost::redis::connection conn{ioctx};  
  
  boost::redis::request request;  
  boost::redis::response<std::string> response;  
  conn.async_exec(request, response, move_only_handler{});  
}  
```  
compilation fails with:  
```  
~/test/redis_exec $ g++ main.cc -Iboost/include -I redis/include  
In file included from redis/include/boost/redis.hpp:12,  
                 from main.cc:2:  
redis/include/boost/redis/connection.hpp: In instantiation of ‘auto boost::redis::basic_connection<Executor>::async_exec(const boost::redis::request&, Response&, CompletionToken) [with Response = std::tuple<boost::system::result<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::redis::adapter::error> >; CompletionToken = move_only_handler; Executor = boost::asio::any_io_executor]’:  
redis/include/boost/redis/connection.hpp:354:30:   required from ‘auto boost::redis::connection::async_exec(const boost::redis::request&, Response&, CompletionToken) [with Response = std::tuple<boost::system::result<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::redis::adapter::error> >; CompletionToken = move_only_handler]’  
main.cc:18:18:   required from here  
redis/include/boost/redis/connection.hpp:232:30: error: use of deleted function ‘move_only_handler::move_only_handler(const move_only_handler&)’  
  232 |       return impl_.async_exec(req, resp, token);  
      |              ~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~  
main.cc:5:3: note: declared here  
    5 |   move_only_handler(const move_only_handler&) = delete;  
      |   ^~~~~~~~~~~~~~~~~  
In file included from redis/include/boost/redis/connection.hpp:10:  
redis/include/boost/redis/detail/connection_base.hpp:556:72: note:   initializing argument 3 of ‘auto boost::redis::detail::connection_base<Executor>::async_exec(const boost::redis::request&, Response&, CompletionToken) [with Response = std::tuple<boost::system::result<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::redis::adapter::error> >; CompletionToken = move_only_handler; Executor = boost::asio::any_io_executor]’  
  556 |    auto async_exec(request const& req, Response& resp, CompletionToken token)  
      |                                                        ~~~~~~~~~~~~~~~~^~~~~  
```
