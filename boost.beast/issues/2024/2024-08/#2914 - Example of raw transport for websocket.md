# #2914 - Example of raw transport for websocket [Closed]

> Username: ValentinNikin  
> Created at: 2024-08-02 18:51:49 UTC  
> Updated at: 2025-02-10 20:19:40 UTC  
> Closed at: 2025-02-10 20:19:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2914  

I have a single thread application which can process TCP connections (some implementation of Reactor pattern).  
I want to add possibility to process websocket connections.  
Let's assume that I have established TCP connection in which I can send data and from which I can receive data through onRecv-callback.  
  
I want to implement some custom transport-adapter to use it TCP-connection with boost::beast websocket.   
I managed to do this using the websocketpp (https://github.com/zaphoyd/websocketpp) library.   
But later I came across an article on the official boost site (https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/design_choices/comparison_to_zaphoyd_studios_we.html) in which says that boost::beast can do the same thing but only better.  
But I have not found a single example that would confirm this. It seems I should implement `AsyncReadStream`, `AsyncWriteStream`, but also no examples of how to do this.  
  
Is it possible to impement custom transport with for beast websocket? And if so, can you share some examples?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-08-02 21:01:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2914#issuecomment-2266140501  

Yes it is possible to implement a custom transport. No, I cannot share an example. It is expected that users are able to read the concept requirements and be able to create a working implementation from those requirements. For reference, here are the requirements that you must implement:  
  
https://www.boost.org/doc/libs/1_85_0/doc/html/boost_asio/reference/AsyncReadStream.html  
https://www.boost.org/doc/libs/1_85_0/doc/html/boost_asio/reference/AsyncWriteStream.html  
  
You will also need to provide an overload of the functions `teardown` and `async_teardown` as explained in the documentation:  
  
https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/using_websocket/teardown.html  
  
If you get stuck and you have specific questions, consider asking in the Official C++ Language Slack Workspace (https://cpp.al/slack)

---

## Comment 2

> Username: ValentinNikin  
> Created at: 2024-08-06 06:26:22 UTC  
> Updated at: 2024-08-06 06:26:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2914#issuecomment-2270480411  

Thank you for the answer. I already tried to implement AsyncReadStream/AsyncWriteStream interfaces. But seems it is not enough for custom transport class.   
  
Below is an example of a maximally simplified class where the necessary methods are declared.  
```  
class NextLayerImpl_ {  
public:  
    using executor_type = boost::asio::any_io_executor;  
    any_io_executor get_executor() const { return ex; }  
  
    template<class MB, class Handler>  
    void async_write_some(MB mb, Handler&& func)  
    {  
  
    }  
  
    template<class MB, class Handler>  
    void async_read_some(MB mb, Handler&& func)  
    {  
  
    }  
  
private:  
    any_io_executor ex;  
};  
  
template<class TeardownHandler>  
void async_teardown(  
    boost::beast::role_type role,  
    NextLayerImpl_& stream,  
    TeardownHandler&& handler) {  
  
    }  
  
void fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
void on_accept(beast::error_code ec)  
{  
    if(ec)  
        return fail(ec, "accept");  
  
    // Read a message  
    std::cout << "On accept called" << std::endl;  
}  
  
int main(int argc, char* argv[])  
{  
    websocket::stream<NextLayerImpl_> ws_;  
  
    ws_.async_accept(  
        beast::bind_front_handler(  
                &on_accept)  
    );  
  
    return EXIT_SUCCESS;  
}  
```  
  
But I got compilation error. Probably the class must satisfy some other requirements. Are these additional requirements written somewhere?  
```  
...  
/usr/local/include/boost/beast/websocket/impl/accept.hpp:568:26:   required from ‘auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(AcceptHandler&&) [with AcceptHandler = boost::beast::detail::bind_front_wrapper<void (*)(boost::system::error_code)>; NextLayer = NextLayerImpl_; bool deflateSupported = true]’  
/home/nikin/workspace/cpp-test/beast/main.cpp:156:21:   required from here  
/usr/local/include/boost/beast/core/stream_traits.hpp:459:27: error: no matching function for call to ‘beast_close_socket(NextLayerImpl_&)’  
  459 |         beast_close_socket(t);  
      |         ~~~~~~~~~~~~~~~~~~^~~  
/usr/local/include/boost/beast/core/stream_traits.hpp:442:1: note: candidate: ‘template<class Protocol, class Executor> void boost::beast::beast_close_socket(boost::asio::basic_socket<Protocol, Executor>&)’  
  442 | beast_close_socket(  
      | ^~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/beast/core/stream_traits.hpp:442:1: note:   template argument deduction/substitution failed:  
/usr/local/include/boost/beast/core/stream_traits.hpp:459:27: note:   ‘NextLayerImpl_’ is not derived from ‘boost::asio::basic_socket<Protocol, Executor>’  
  459 |         beast_close_socket(t);  
      |         ~~~~~~~~~~~~~~~~~~^~~  
```

---

## Comment 3

> Username: ashtum  
> Created at: 2024-08-06 07:07:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2914#issuecomment-2270540619  

You need to provide an overload of the function [beast_close_socket](https://www.boost.org/doc/libs/1_85_0/libs/beast/doc/html/beast/ref/boost__beast__beast_close_socket.html) too:  
```C++  
void  
beast_close_socket(NextLayerImpl_&)  
{  
}  
```

---

## Comment 4

> Username: ValentinNikin  
> Created at: 2024-08-06 08:31:15 UTC  
> Updated at: 2024-08-06 08:31:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2914#issuecomment-2270693777  

@ashtum Thank you, this helped to compile the application.  
  
Could you tell me concrete types for MB and Handler? I need to save this parameters as class fields. I need this to fill them out later, when I got data from inner transport infrastructure.  
  
```  
template<class MB, class Handler>  
auto async_read_some(MB buf, Handler&& func)  
```  
  
I tried to use boost::beast::detail::BufferSequence<boost::asio::mutable_buffer> instead of MB, but got a compilation error  
```  
/usr/local/include/boost/beast/websocket/impl/accept.hpp:568:26:   required from ‘auto boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::async_accept(AcceptHandler&&) [with AcceptHandler = boost::beast::detail::bind_front_wrapper<void (*)(boost::system::error_code)>; NextLayer = NextLayerImpl_; bool deflateSupported = true]’  
/home/nikin/workspace/cpp-test/beast/main.cpp:204:21:   required from here  
/usr/local/include/boost/beast/http/impl/read.hpp:600:48: error: static assertion failed: AsyncReadStream type requirements not met  
  600 |         is_async_read_stream<AsyncReadStream>::value,  
      |                                                ^~~~~  
/usr/local/include/boost/beast/http/impl/read.hpp:600:48: note: ‘std::integral_constant<bool, false>::value’ evaluates to false  
```

---

## Comment 5

> Username: ashtum  
> Created at: 2024-08-06 09:37:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2914#issuecomment-2270838307  

The Handler is not a regular function object; it is an Asio [Completion Token](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/overview/model/completion_tokens.html). You can only use it through [async_initiate](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/async_initiate.html). You can use [any_completion_handler](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/any_completion_handler.html) for type-erasing the handler, but it still needs to go through async_initiate first. Here is an example: https://github.com/boostorg/asio/tree/develop/example/cpp11/type_erasure.  
  
Regarding the buffer sequence types, there is no concrete type for them. They have different types in different operations, and you need to create a type-erased wrapper for the [Mutable buffer sequence](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/MutableBufferSequence.html) and [Constant buffer sequence](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/ConstBufferSequence.html) concepts.

---

## Comment 6

> Username: ValentinNikin  
> Created at: 2024-08-06 10:18:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2914#issuecomment-2270922868  

@ashtum I will try this, thank you for the reply
