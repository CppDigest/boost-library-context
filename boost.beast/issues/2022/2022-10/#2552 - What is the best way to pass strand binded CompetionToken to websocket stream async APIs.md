# #2552 - What is the best way to pass strand binded CompetionToken to websocket stream async APIs [Closed]

> Username: redboltz  
> Created at: 2022-10-28 04:06:09 UTC  
> Updated at: 2022-10-28 07:07:03 UTC  
> Closed at: 2022-10-28 07:07:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2552  

### Version of Beast  
  
BOOST_BEAST_VERSION 330  
BOOST_VERSION 108000  
  
### Steps necessary to reproduce the problem  
When I call beast websocket stream APIs, I want to strand binded CompletionToken.  
If I pass the strand `strand1` that is made by non type erased executor, it works fine.  
  
However, if I passed strand `strand2`, `strand3`, or `strand4`, then I got compile error.  
  
```  
/opt/compiler-explorer/libs/boost_1_80_0/boost/asio/strand.hpp:251:15: error: no member named 'on_work_started' in 'boost::asio::any_io_executor'  
    executor_.on_work_started();  
    ~~~~~~~~~ ^  
```  
  
It seems that the error is caused by gotten executor is type erased any_io_executor.  
I can preserve the original `ioc` reference and use it to make strand in my code. But I guess that there is more elegant way.  
  
Could you tell me what is the good way to pass strand binded CompletionToken to websocket stream async_* APIs ?  
  
godbolt link: https://godbolt.org/z/q4rxY6Wsr  
  
```cpp  
#include <iostream>  
  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
  
using ws_stream_t = boost::beast::websocket::stream<boost::asio::ip::tcp::socket>;  
  
void test() {  
    boost::asio::io_context ioc;  
    ws_stream_t stream{ioc.get_executor()};  
  
    std::string buf = "ABC";  
  
    auto cb = [](boost::system::error_code const& ec, std::size_t bytes_transferred) {};  
  
    // bind strand by bind_executor  
  
    // success ioc.get_executor() return type is not type erased  
    auto strand1 = boost::asio::make_strand(ioc.get_executor());  
    stream.async_write(boost::asio::buffer(buf), boost::asio::bind_executor(strand1, cb));  
  
  
    // fail their get_executor() return type is any_executor.  
    // dut to type erasing, cannot call on_work_started()  
#if 1  
    auto strand2 = boost::asio::make_strand(stream.get_executor());  
    stream.async_write(boost::asio::buffer(buf), boost::asio::bind_executor(strand2, cb));  
  
    auto strand3 = boost::asio::make_strand(stream.next_layer().get_executor());  
    stream.async_write(boost::asio::buffer(buf), boost::asio::bind_executor(strand3, cb));  
  
    auto strand4 = boost::asio::make_strand(boost::beast::get_lowest_layer(stream).get_executor());  
    stream.async_write(boost::asio::buffer(buf), boost::asio::bind_executor(strand4, cb));  
#endif  
}  
  
int main() {  
    std::cout << BOOST_VERSION << std::endl;  
    std::cout << BOOST_BEAST_VERSION << std::endl;  
}  
```  
  
NOTE: The demonstration code is focued on compile error. Call async_write() without connecting is not essential part of the problem.  
  
### All relevant compiler information  
  
Compiler: x86-64 clang 15.0.0  
Option: `-O0 -std=c++20 -pthread`

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-28 04:39:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2552#issuecomment-1294445596  

You can either make stream use another executor, e.g.:  
  
```cpp  
using ws_stream_t = boost::beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::io_context::executor_type>;  
```  
  
or you can get the underlying strand from the any_io_executor with `.target`.   
Note that a stand is a valid executor, i.e. can be assigned to `any_io_executor`.

---

## Comment 2

> Username: redboltz  
> Created at: 2022-10-28 07:07:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2552#issuecomment-1294553607  

Thank you! I use the first solution.
