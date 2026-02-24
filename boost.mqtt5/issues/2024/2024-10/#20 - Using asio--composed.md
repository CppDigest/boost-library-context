# #20 - Using asio::composed [Closed]

> Username: ashtum  
> Created at: 2024-10-22 15:22:38 UTC  
> Updated at: 2024-10-23 17:42:28 UTC  
> Closed at: 2024-10-23 17:42:28 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/20  

> Async.MQTT5 addresses this by using a construct that launches composed operations via the  
lower-level async_initiate. Once launched, the composed operation serially runs intermediate  
asynchronous operations, supplying itself as the completion handler for each. However, for each  
intermediate operation, the composed operation defines a unique operator() overload with a  
type-distinct first argument, while the remaining argument types match the expected signature for  
each intermediate operation’s completion handler. The correct operator() overload is then  
selected by binding this distinct first argument to an instance of its respective type.  
  
Isn't this possible even with composed operations?  
https://godbolt.org/z/xx1o8Ghxa  
```C++  
#include <boost/asio.hpp>  
  
namespace asio = boost::asio;  
  
struct async_echo_implementation  
{  
    asio::ip::tcp::socket& socket_;  
    asio::mutable_buffer buffer_;  
  
    struct starting{};  
    struct reading{};  
    struct writing{};  
  
    template<typename Self>  
    void  
    operator()(Self& self, starting)  
    {  
        socket_.async_read_some(  
            buffer_, asio::prepend(std::move(self), reading{}));  
    }  
  
    template<typename Self>  
    void  
    operator()(  
        Self& self,  
        reading,  
        boost::system::error_code error,  
        std::size_t n)  
    {  
        if(error)  
        {  
            self.complete(error, 0);  
        }  
        else  
        {  
            asio::async_write(  
                socket_,  
                buffer_,  
                asio::transfer_exactly(n),  
                asio::prepend(std::move(self), writing{}));  
        }  
    }  
    template<typename Self>  
    void  
    operator()(  
        Self& self,  
        writing,  
        boost::system::error_code error,  
        std::size_t n)  
    {  
        self.complete(error, n);  
    }  
};  
  
template<typename CompletionToken>  
auto  
async_echo(  
    asio::ip::tcp::socket& socket,  
    asio::mutable_buffer buffer,  
    CompletionToken&& token)  
{  
    return asio::async_initiate<  
        CompletionToken,  
        void(boost::system::error_code, std::size_t)>(  
        asio::composed(async_echo_implementation{ socket, buffer }, socket),  
        token,  
        async_echo_implementation::starting{});  
}  
  
int  
main()  
{  
    asio::io_context ioc;  
    asio::ip::tcp::socket socket{ ioc };  
  
    async_echo(socket, asio::mutable_buffer{}, asio::detached);  
    ioc.run();  
}  
```

---

## Comment 1

> Username: siladic  
> Created at: 2024-10-22 19:38:45 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/20#issuecomment-2430099712  

async_compose can leverage the same `asio::prepend/append` trick we use. However, with `async_compose`, you don't control how the executor, allocator, and cancellation slot are propagated to lower async operations. `async_compose` will pass to lower async operations the executor, allocator, and cancellation slot that were attached (if any) to the outer completion token. Sometimes, this is not desirable, as you may want to control, for example, which cancellation slot gets propagated to the lower async operation.  
  
This is especially important when you need to implement a "total cancellation" type, where you may not want to immediately cancel a lower `async_write` operation but instead cancel the outer handle while allowing the underlying operation to continue. This scenario is particularly relevant in the case of total cancellation, such as with WebSocket. The same happens with MQTT—you must finish the conversation cleanly with the broker, even if someone abruptly cancels the PUBLISH operation in the middle of its execution.  
  
The ability to control exactly what is propagated to lower-level async operations is the main reason we use `async_initiate` instead of `async_compose`. In many cases within the Async.MQTT5 library, `async_compose` and `async_initiate` result in the same behavior, but they differ in key areas. We use `async_initiate` to maintain consistency throughout.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-10-23 17:42:28 UTC  
> Url: https://github.com/boostorg/mqtt5/issues/20#issuecomment-2432981807  

Thanks for the explanation. That really cleared things up.
