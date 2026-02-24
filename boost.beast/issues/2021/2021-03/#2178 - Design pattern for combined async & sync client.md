# #2178 - Design pattern for combined async & sync client [Closed]

> Username: Tectu  
> Created at: 2021-03-04 10:03:48 UTC  
> Updated at: 2021-03-16 11:59:06 UTC  
> Closed at: 2021-03-16 11:59:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2178  

Using beast, I wrote a simple server based on the websocket async-server example. I've also created a corresponding client based on the async-client and sync-client examples.  
  
My client contains a class `controller` which is currently hard-coded to use the async-session:  
```cpp  
class controller  
{  
   // ...  
private:  
   session_async m_session;  
};  
```  
  
I would like to modify the `controller` class in a way that allows the client/user of this class to specify whether to use the synchronous or asynchronous session when talking to the server.  
In simplest terms, something like this:  
```cpp  
template<typename Session>  
class controller  
{  
    // ....  
private:  
    Session m_session;  
};  
```  
  
My question: Are there existing, known design patterns to do something like this? Does `beast` provide anything to facilitate this?  
How would one classically go about implementing this? I'm sure I'm not the first one doing this :)  
  
Any kind of input, feedback, design patterns, pointers, tutorials, explanations, guides etc. are highly welcomed.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-04 12:05:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-790570273  

Hi there.  
  
You're not the first and there are a couple of ways to do this.  
  
Vinnie prefers to write a polymorphic concept representing the transport layer, which would have methods such as `send_frame()`, `on_frame_received` and `on_error`.  
  
Personally I prefer to create a `variant_websocket` which would be along the lines of :  
```  
using tcp_layer = beast::tcp_stream;  
using ssl_layer = asio::ssl::stream<tcp_layer>;  
using ws_stream = beast::websocket::stream<tcp_layer>;  
using wss_stream = beast::websocket::stream<ssl_layer>;  
using variant_websocket = variant<ws_stream, wss_stream>;  
```   
  
High level asynchronous operations against the variant stream then become:  
  
```  
template<class DynBuf, class CompletionToken>  
auto async_read(variant_websocket& vws, DynBuf& dynbuf, CompletionToken&& handler)  
{  
  auto op =[&](auto& stream)   
  {  
    return stream.async_read(dynbuf,   
                             std::forward<CompletionToken>(handler));  
  };  
  
  return visit(op, vws);  
}  
```  
  
You would want to hold the variant_websocket in an optional because you don't know which version you will need until you have decoded the URL.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-03-04 12:20:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-790578575  

Here's an example I wrote for one of my blogs  
https://github.com/madmongo1/blog-new-year-2021/blob/master/src/http/variant_stream.hpp  
  
and here's an example of how Vinnie prefers to structure it:  
https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp

---

## Comment 3

> Username: Tectu  
> Created at: 2021-03-04 12:54:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-790596871  

Thank you for your reply.  
I'm not sure whether maybe I was not clear or whether I don't understand the relevance of the provided examples. I am (at least currently) not looking to implement communication that can either work in play or TLS. Instead, I want to create a piece of high-level code which communicates to the server via the socket (no matter whether plain or TLS) in either synchronous or asynchronous mode.  
  
My `controller` class looks like this:  
```cpp  
#pragma once  
  
namespace boost::asio  
{  
    class io_context;  
}  
  
namespace proto::request  
{  
    class top_level;  
}  
  
namespace client  
{  
    class session_async;  
    class session_sync;  
  
    class controller  
    {  
    public:  
        // Construction  
        controller(boost::asio::io_context& io_ctx);  
        controller(const controller& other) = delete;  
        controller(controller&& other) noexcept = delete;  
        virtual ~controller() = default;  
  
        // Operators  
        controller& operator=(const controller& rhs) = delete;  
        controller& operator=(controller&& rhs) noexcept = delete;  
  
        // General  
        void connect(const std::string& host, const std::string& port);  
        void disconnect();  
        [[nodiscard]] bool is_connected() const;  
        void do_something();  
        void do_something_else();  
        void do_something_different();  
  
    private:  
        std::shared_ptr<session_sync> m_session;  
  
        std::string send_request(const proto::request::top_level& request);  
    };  
}  
```  
This `controller` class is a higher-level construct which gets used by a user of the library I am writing to communicate to the existing server. The various `do_something*()` methods construct and send protobuf messages over the session so the user of the class does not need to know details about the communication protocol (eg. the protobuf messages). As you can see, currently the `controller` has a private member of type `session_sync`. My goal is to modify this class in a way that allows the user of the class to choose (during compile time) whether the communication should happen using `session_sync` or `session_async`.  
  
The existing `controller::send_request()` method is right now simply waiting in a synchronous manner (due to the use of `session_sync` for the response of the server.  
If the user of the class decides to use an asynchronous communication model instead (using `session_async`), this function needs to behave differently.  
  
As per my understanding there are two problem to solve:  
  - Give the user the ability to select the communication model. This can most likely be done by passing the session as a template parameter.  
  - Modify the internals of `controller` in a way that it will work with either `session_async` or `session_sync`. This would require modifications of the `controller::send_request()` function. I assume that in asynchronous mode I'll have to add some kind of message identifier to the request which the server will echo back in the response so the `controller` has a way to identify to which request any given response belongs to.  
  
My question is whether there are existing solutions to do this (either in form of design patterns, guidelines or utility classes).  
  
In case you understood my question already this way the first time I'll unfortunately need some more information of how the provided examples are relevant.  
  
Furthermore: Can you provide a link to your blogpost related to the example you linked?  
  
Thank you for your efforts - much appreciated.

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-03-04 13:52:02 UTC  
> Updated at: 2021-03-04 13:53:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-790632516  

> either synchronous or asynchronous mode  
  
Woops I got wildly off track there.  
  
I have implemented something similar to this just once before in a commercial server. I don't think there is a standard idiom for it.  
  
What I did was to derive entities (in your case sessions) from one of two interfaces, async and sync. The caller would query for each interface and use the one it found first.  
  
Not quite what you want.  
  
What you're looking to do is sort-of modelled by the generated protobuf RPC code (unless that's been deprecated now, it's been a while since I used it).  
  
I can imagine another way would be to provide an executor model that performs in-line execution.  
  
You could then write all code in terms of async operations on the executor as if always async. When run against a standard asio executor, operation would be async. When run against your custom straight-through executor, you'd get synchronous operation.  
  
I don't imagine this would interoperate well asio's read/write/wait etc   
  
Usually programs are either sync or async. There are implementation differences that matter.  
  
What's the actual motivating use case here?  
  
re blogs: [https://cppalliance.org/richard/2021/01/01/RichardsNewYearUpdate.html ](https://cppalliance.org/richard/2021/01/01/RichardsNewYearUpdate.html )

---

## Comment 5

> Username: Tectu  
> Created at: 2021-03-04 14:31:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-790659352  

> [...] generated protobuf RPC code (unless that's been deprecated now, it's been a while since I used it).  
  
I am using "bare" protobuf 3. I looked into gRPC but after some evaluation determined that the dependencies it pulls in are too big of a price to pay for this particular project/situation. But yeah: I want to build something that behaves like gRPC (from a system point of view) - but not using gRPC. The functionality that needs to be supported is very limited and there will be less than a dozen different "actions" a client can issue. The trade-off in maintainability from pulling in gRPC appears too big.  
  
> I can imagine another way would be to provide an executor model that performs in-line execution.  
>   
> You could then write all code in terms of async operations on the executor as if always async. When run against a standard asio executor, operation would be async. When run against your custom straight-through executor, you'd get synchronous operation.  
  
That indeed sounds more like what I was looking for. However, I understand that this is "an unusual approach". I'll share my use case as you might be able to provide a better solution below.  
  
> What's the actual motivating use case here?  
  
I am currently writing a "large" C++ library which provides certain services (doesn't really matter what those are). A small part of that library is a server designed around `boost::beast` which allows to use those services via websockets.  
As with any good library there is also a corresponding client that will ship with it. The idea is that a user of the library can simply use the client to write a piece of software which can communicate with the server which eventually uses the "large part" of the library to implement/provide the services. The client itself is part of the library - not a finished executable in order to allow for easy inclusion into a different program (whatever application the user of the library wants to add these capabilities to).  
  
I foresee situations where a user of the library might like to use the client that gets embedded into his/her application in either synchronous or asynchronous mode. For example, one might build a very simple CLI application that performs just one task and that's it. On other occasions, one might want to built a fully-fledged management application around this which would benefit/require asynchronous communications.  
  
The client code that comes with the library should be as generic and as reusable as possible. Therefore, I'd like to write it in a way that allows choosing whether to use synchronous or asynchronous communication with the server.  
  
I hope this is understandable - please let me know if I need to add more clarity/information/details.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-03-04 23:42:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-791033343  

Would it be fair to say that you are sometimes wanting to wrap some sync operations in async initiation/completion and wrap some async operations in synchronous wrappers?

---

## Comment 7

> Username: Tectu  
> Created at: 2021-03-05 09:59:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-791312072  

That would be not only fair but also accurate.

---

## Comment 8

> Username: KnoerleMan  
> Created at: 2021-03-05 18:30:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-791602317  

Without further knowledge about your classes i would take the following approach:  
  
Create a base class and make "session_async" and "session_sync" a derived class of that base class.  
Afterwards the user of the client library should be forced to create the desired type of session (sync/async), e.g. via a factory method returning an shared_ptr of the session.  
  
Afterwards the controller class should hold a `std::shared_ptr<session_base>`, which has to be passed on construction.

---

## Comment 9

> Username: Tectu  
> Created at: 2021-03-08 19:06:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-792999189  

Thank you for all your inputs.  
  
After evaluating the various options I decided that supporting both an asynchronous and a synchronous session is not the best design decision.  
  
I will instead built a wrapper around the existing asynchronous session that will provide API to inform the user whether there are still responses expected from the server.  
  
If this is a very bad idea I'm open for more feedback & information on the topic.

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-03-08 19:17:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-793005931  

> API to inform the user whether there are still responses expected from the server  
  
Were you thinking of an api such as   
  
```  
wrapper.async_wait(completion_handler);  // executes on the handler's executor  
// this would allow things such as  
// co_await wrapper.wait(asio::use_awaitable); , or  
// wrapper.wait(asio::use_future).wait();   // blocks until no tasks remaining  
```  
  
or   
  
```  
wrapper.on_complete(completion_handler); // executes in the context of some defined executor, such as the wrapper's executor  
```  
?

---

## Comment 11

> Username: Tectu  
> Created at: 2021-03-09 12:22:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-793808375  

I am not sure how to answer that question appropriately. I would tend towards your second example. Basically giving the user the ability to issue a request - which will be handled through the asynchronous session - and then a user defined completion handler will be called.  
  
What I had in mind is something along the lines of:  
```cpp  
client.make_some_request(completion_handler);  
```  
  
Here `make_some_request()` is just one of many different requests that the user may issue. The signature of the `completion_handler` will be such that the information returned by the server (specific to that request) will be passed via function parameters to the completion handler.  
  
What are your thoughts on this?

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-03-09 12:46:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-793840781  

My thoughts on this is that asio already has a pattern for this.  
It takes a little getting used to but the general form is used throughout beast and asio.  
Initiating asynchronous requests like this is most easily done with either a c++ coroutine (which then makes your code specific to coroutines) or using asio's `async_compose` to initiate and own the asynchronous composed operation.  
Async compose (and the lower-level async_initiate) deduce the return type (future, void, awaitable, etc) from the type of the `completion_handler`.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2021-03-11 20:24:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2178#issuecomment-797024799  

> supporting both an asynchronous and a synchronous session is not the best design decision.  
  
This is true.
