# #226 - async_write with Future && async result [Closed]

> Username: basiliscos  
> Created at: 2019-04-19 22:10:13 UTC  
> Updated at: 2020-12-30 01:03:19 UTC  
> Closed at: 2020-12-30 01:03:18 UTC  
> Url: https://github.com/boostorg/asio/issues/226  

Hi,   
  
the following code works on boost 1.70, but on 1.69 it throws with `std::future_error: Future already retrieved` .   
  
I cannot see any reference to in the changelog that it was fixed, and still not completely sure the code sample is correct.  
  
Could you, please, leave a comment?  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/asio/use_future.hpp>  
#include <future>  
#include <iostream>  
  
namespace asio = boost::asio;  
  
template<typename Stream, typename Buff, typename CompletionToken>  
auto async_op(Stream &s, Buff&& buff, CompletionToken&& ct) {  
    using Signature = void(const boost::system::error_code&, std::size_t);  
    using Callback = std::decay_t<CompletionToken>;  
    using AsyncResult = asio::async_result<Callback, Signature>;  
    using CompletionHandler = typename AsyncResult::completion_handler_type;  
  
    CompletionHandler handler(std::forward<CompletionToken>(ct));  
    AsyncResult result(handler);  
    async_write(s, std::move(buff), std::move(handler));  
    return result.get();  
}  
  
int main(int argc, char** argv) {  
    using socket_t = asio::ip::tcp::socket;  
  
    asio::io_service io_service;  
  
    asio::ip::tcp::resolver resolver(io_service);  
    socket_t sock(io_service);  
    auto endpoints = resolver.resolve("google.com", "https");  
    sock.connect(*endpoints.begin());  
    char buff[] = "does-not-matter";  
  
    auto f_write = async_op(sock, asio::buffer(buff), asio::use_future);  
    //auto f_write = async_write(sock, asio::buffer(buff), asio::use_future);  
    std::thread thread([&io_service]() { io_service.run(); });  
  
    std::cout << "r = " << f_write.get() << "\n";  
  
    io_service.stop();  
    thread.join();  
  
    return  0;  
}  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-20 17:33:58 UTC  
> Url: https://github.com/boostorg/asio/issues/226#issuecomment-485145647  

hmm.... I have no idea!

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:03:17 UTC  
> Url: https://github.com/boostorg/asio/issues/226#issuecomment-752291912  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#684](https://github.com/chriskohlhoff/asio/issues/684).
