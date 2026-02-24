# #2798 - ecorm's List of Pain Points [Closed]

> Username: ecorm  
> Created at: 2024-01-12 17:54:08 UTC  
> Updated at: 2024-01-13 22:11:55 UTC  
> Closed at: 2024-01-12 17:55:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2798  

As I'm using this library, I'm discovering pain points that could be improved upon from a user's perspective. Since this library is now in maintenance mode, and will presumably will not be incorporating any new features, I will add these pain points here instead of creating a new issue every time. If I encounter genuine bugs, I will create an issue.  
  
This is not intended to diminish this excellent library or its author. It is intended to be referenced in future endeavors so that these pain points are at least considered, if not addressed.  
  
---  
### 1. Parsers, serializers, and buffers are not resettable  
  
In a networking app, these objects should be reusable over and over, instead of constructing them from scratch every time. When storing them as class members, the suggested workaround is to wrap them in `boost::optional` or `std::optional`.  
  
---  
### 2. Closing or destroying a websocket::stream should always be safe.  
  
The [websocket::stream destructor documentation](https://www.boost.org/doc/libs/release/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/_dtor_stream.html) has this note:  
  
> A stream object must not be destroyed while there are pending asynchronous operations associated with it.  
  
What Asio does when destroying/closing sockets is that is immediately cancels all pending operations.  
  
The current `websocket::stream::close` method should be renamed to something else that indicates that the operation is not immediate. `websocket::stream::close` should have the same immediate-effect semantics as for Asio sockets.  
  
---  
### 3. The state of `next_layer` stream is unknown after `websocket::stream::close` completes.  
  
I brought this up in #2730. Is the underlying socket still open when `websocket::stream::close` completes? The documentation doesn't say.  
  
---  
### 4. No `websocket::stream::async_wait` method  
  
I brought this up in #2773. I want to receive a notification that data is available to be read, just like in `asio::ip::tcp::socket::async_wait`. As a workaround, I do `websocket::async_read_some` with a limit of a single byte.  
  
---  
### 5. Incremental HTTP reads are a bit convoluted.  
  
I brought this up in #2797. Incremental HTTP writes are much simpler by comparison and I think incremental reads should be just as easy with a `limit` parameter injected somewhere.  
  
---  
### 6. No type-erased HTTP message wrapper  
  
I can't easily reuse my incremental HTTP write logic (using `http::async_write_some`) with various response types. Some responses have `file_body`, whereas others have `string_body`.  
  
There is no `async_write_some` function that takes the type-erased `http::message_generator`, so I'm going to have to write my own polymorphic incremental HTTP writer class.  
  
---  
### 7. `websocket::stream` is not move-assignable  
  
This has given me problems in implementing move semantics in the the higher-level objects containing the `websocket::stream` (e.g. transferring the websocket stream from a "listener" object to a "session" object). The workaround I used involved using `boost::optional` and implementing explicit move constructor/assignment operators.  
  
---  
### 8. HTTP incremental writes is clamped to hard-coded 4096 bytes for `file_body`  
  
I had configured HTTP incremental writes (using `http::async_write_some`) to use a limit of 8kB, and was puzzled as to why it was only sending 4kB at a time. Stepping with the debugger led me to `basic_file_body::writer::get`, where the size is clamped to `BOOST_BEAST_FILE_BUFFER_SIZE`, which is hard-coded to 4096. This can of course be changed at compile-time but it cannot be set at runtime.  
  
---  
### 9. No Range support for `file_body`  
  
`response<file_body>` cannot be used to send only a sub-range of the file data. [Range requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests) are useful for on-demand video streaming, for example. As a workaround, one can load the requested data range manually from the file, and send it via a `response<string_body>` or `response<vector_body>`.

---

## Comment 1

> Username: ecorm  
> Created at: 2024-01-12 17:55:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1889724680  

Closing immediately as these are not bugs.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-01-12 18:33:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1889772858  

Some of these points are indeed valid.  
  
1. Yep the lack of resettability is ANNOYING  
  
2. It might be safe to destroy now, that the stream uses a shared pointer to an implementation object. @ashtum can you investigate here:  
https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/include/boost/beast/websocket/stream.hpp#L134  
  
3. The documentation does say that the "teardown" operation is performed on the next layer. So it is very well defined (even to the point where you can define shutdown for your own stream types). Is this unclear?  
https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/using_websocket/teardown.html  
  
4. This is a weird way to use the socket. The stream is designed for there to always be a pending call to `async_read` of some sort, unless the caller is busy and wants TCP/IP's application-level flow control to slow things down. You are supposed to always be reading when you aren't busy.  
  
5. I agree incremental reads are convoluted.  
  
6. This is probably the biggest design flaw with Beast. The body type is baked into the message type, there are too many template parameters, and the parser is templated on the body type. Fixing this is pretty much impossible but I am designing a new library that uses a different approach. See: https://github.com/cppalliance/http_proto/  
  
Thanks

---

## Comment 3

> Username: ecorm  
> Created at: 2024-01-12 19:03:36 UTC  
> Updated at: 2024-01-12 19:13:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1889807841  

> 3. The documentation does say that the "teardown" operation is performed on the next layer. So it is very well defined (even to the point where you can define shutdown for your own stream types). Is this unclear?  
https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/using_websocket/teardown.html  
  
- There's no mention of "teardown" operations in the `websocket::stream::close` documentation, so one needs to have a least glanced at the entire documentation to be aware that his mechanism exists, and _remember_ that it exists. I have 1485 other things I have to constantly keep in my head, so it's easy for me to forget that `websocket::stream::close` performs a customizable teardown operation. :grin:  Just a note with a link to the Teardown page in the `websocket::stream::close` documentation should suffice.  
- It's not clear if the teardown operation is completed or is kicked off by time the `websocket::stream::async_close` handler is invoked.  
- The Teardown documentation page does not describe what the default teardown operation actually does. ~One can guess~ It sorta implies that the default teardown is per RFC6455 section 7.1.1., but it would be better if this were explicitly stated.  
- It's not clear what state the underlying TCP socket is in if the `websocket::stream::close` operation fails. With Asio, `ip::tcp::socket::close` is a no-fail operation and I can count on the socket being actually closed from my perspective.  
  
> 4. This is a weird way to use the socket. The stream is designed for there to always be a pending call to `async_read` of some sort, unless the caller is busy and wants TCP/IP's application-level flow control to slow things down. You are supposed to always be reading when you aren't busy.  
  
I need to use the socket this way to distinguish idle timeouts from read timeouts.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-01-12 20:18:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1889898238  

Yes these things could be documented, please open an issue

---

## Comment 5

> Username: ecorm  
> Created at: 2024-01-12 21:03:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1889945935  

> Yes these things could be documented, please open an issue  
  
Bumped issue #2730 I had already opened and gave it a better name.

---

## Comment 6

> Username: ecorm  
> Created at: 2024-01-12 23:44:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890150214  

Added points number 7 thru 9.

---

## Comment 7

> Username: ecorm  
> Created at: 2024-01-13 00:14:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890169232  

Elaborating on point 6, here are the helper classes I had to write in order to get the same `http::async_write_some` algorithm to work with different body types. I release them onto the public domain. Please forgive the use of PascalCase and camelCase.  
  
```c++  
namespace detail  
{  
  
//------------------------------------------------------------------------------  
class HttpSerializerBase  
{  
public:  
    using Socket = boost::asio::ip::tcp::socket;  
    using Handler = boost::asio::any_completion_handler<void (boost::beast::error_code,  
                                                        std::size_t)>;  
  
    virtual ~HttpSerializerBase() = default;  
  
    virtual void asyncWriteSome(Socket& tcp, Handler handler) = 0;  
  
    virtual bool done() const = 0;  
  
protected:  
    HttpSerializerBase() = default;  
};  
  
  
//------------------------------------------------------------------------------  
template <typename R>  
class PolymorphicHttpSerializer : public HttpSerializerBase  
{  
public:  
    using Response = R;  
  
    template <typename T>  
    explicit PolymorphicHttpSerializer(T&& response, std::size_t limit)  
        : response_(std::forward<T>(response)),  
          serializer_(response_)  
    {  
        serializer_.limit(limit);  
    }  
  
    void asyncWriteSome(Socket& tcp, Handler handler) override  
    {  
        // A lambda could be used instead in C++14 with move captures  
        struct Written  
        {  
            Handler handler;  
  
            void operator()(boost::beast::error_code netEc, std::size_t n)  
            {  
                handler(netEc, n);  
            }  
        };  
  
        boost::beast::http::async_write_some(tcp, serializer_,  
                                             Written{std::move(handler)});  
    }  
  
    bool done() const override  
    {  
        return serializer_.is_done();  
    }  
  
private:  
    using Body = typename Response::body_type;  
    using Fields = typename Response::fields_type;  
    using Serializer = boost::beast::http::serializer<false, Body, Fields>;  
  
    Response response_;  
    Serializer serializer_;  
};  
  
} // namespace detail  
  
//------------------------------------------------------------------------------  
// Type-erases a boost::beast::http::serializer so that the same incremental  
// write algorithm can work with any response body type.  
//------------------------------------------------------------------------------  
class AnyHttpSerializer  
{  
public:  
    using Socket = boost::asio::ip::tcp::socket;  
    using Handler = boost::asio::any_completion_handler<void (boost::beast::error_code,  
                                                        std::size_t)>;  
  
    AnyHttpSerializer() = default;  
  
    template <typename R>  
    AnyHttpSerializer(R&& response, std::size_t limit)  
        : serializer_(makeSerializer(std::forward<R>(response), limit))  
    {}  
  
    explicit operator bool() const {return empty();}  
  
    bool empty() const {return serializer_ == nullptr;}  
  
    bool done() const {return empty() ? true : serializer_->done();}  
  
    void reset() {serializer_.reset();}  
  
    template <typename R>  
    void reset(R&& response, std::size_t limit)  
    {  
        serializer_.reset(makeSerializer(std::forward<R>(response), limit));  
    }  
  
    void asyncWriteSome(Socket& tcp, Handler handler)  
    {  
        assert(!empty());  
        serializer_->asyncWriteSome(tcp, std::move(handler));  
    }  
  
private:  
    template <typename R>  
    static detail::PolymorphicHttpSerializer<typename std::decay<R>::type>*  
    makeSerializer(R&& response, std::size_t limit)  
    {  
        using T = typename std::decay<R>::type;  
        return new detail::PolymorphicHttpSerializer<T>(std::forward<R>(response),  
                                                        limit);  
    }  
  
    std::unique_ptr<detail::HttpSerializerBase> serializer_;  
};  
```  
Usage:  
```c++  
class Session : public std::enable_shared_from_this<Session>  
{  
public:  
    template <typename R>  
    void respond(R&& response)  
    {  
        serializer_.reset(std::forward<R>(response), incrementalWriteLimit_);  
        sendMore();  
    }  
  
private:  
    void sendMore()  
    {  
        auto self = shared_from_this();  
        serializer_.asyncWriteSome(  
            socket_,  
            [this, self](boost::beast::error_code ec, std::size_t n)  
            {  
                if (ec)  
                    throw boost::system::system_error{ec};  
  
                if (serializer_.done())  
                    onSendComplete();  
                else  
                    sendMore();  
            }  
    }  
  
    void onSendComplete()  
    {  
    }  
  
    AnyHttpSerializer serializer_;  
    boost::asio::ip::tcp::socket socket_;  
    std::size_t incrementalWriteLimit_;  
}  
```

---

## Comment 8

> Username: ashtum  
> Created at: 2024-01-13 11:04:47 UTC  
> Updated at: 2024-01-13 13:54:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890418994  

> 2. It might be safe to destroy now, that the stream uses a shared pointer to an implementation object. @ashtum can you investigate here:  
>    https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/include/boost/beast/websocket/stream.hpp#L134  
  
Composed operations keep a weak_ptr to the implementation object. The first action in their call operator is an attempt to create a shared_ptr from it. If this fails, the operation complete with `error::operation_aborted`:  
https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/include/boost/beast/websocket/impl/read.hpp#L85-L91  
  
This was implemented as part of this commit: [Add websocket::stream timeouts](https://github.com/boostorg/beast/commit/f21358186ecad9744ed6c72618d4a4cfc36be5fb).  
  
The problem is the read and write buffers owned by the implementation object, if we destroy the implementation object, the ongoing read and write operations will still reference the same memory location.  
This is problematic because, during asynchronous read/write operations, the actual read/write to those memory regions occurs on the operating system side, and it remains unaware of any actions taken by the caller. It is the caller's responsibility to ensure the validity of the buffers until the operations are completed. This is explicitly stated in Asio's documentation:  
  
> ... ownership of the underlying memory blocks is retained by the caller, which must guarantee that they remain valid until the completion handler is called.  
  
Therefore, although it is possible to destroy a socket object in Asio at any time, the caller must ensure the validity of the buffer used by all outstanding asynchronous operations until they are completed.

---

## Comment 9

> Username: ecorm  
> Created at: 2024-01-13 13:50:16 UTC  
> Updated at: 2024-01-13 14:02:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890464020  

> Therefore, while it is possible to destroy a socket object in Asio at any time, the caller must ensure the validity of the buffer used by those operations until all outstanding asynchronous operations have been completed.  
  
I don't pretend to understand the internals of Boost.Beast, but it seems to me the best approach upon `websocket::stream` destruction would to be to close the underlying Asio stream so that all outstanding async operations are immediately cancelled.  
  
If `websocket::stream` has its own asynchronous operations not done via the underlying Asio stream, then those should also be immediately cancelled.  
  
I propose that a new `websocket::stream` method, named `terminate` (or whatever), be added that performs immediate cancellation and cleanup operations, and does not bother sending a  Websocket Close frame. The `websocket::stream` destructor would just simply call this proposed `terminate` method.  
  
What I'm currently doing now when I want `websocket::stream` to immediately halt is call `websocket.next_layer().close()`.   
  
**Edit**: In addition, whatever buffers are referenced by the OS operations are keep alive via the usual `shared_from_this` idiom, but at least I can count on the pending asynchronous operations ending soon (due to cancellation) and releasing the `shared_ptr`s captured via their completion handlers.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2024-01-13 16:53:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890591321  

> I propose that a new `websocket::stream` method, named `terminate`  
  
No I don't think that's a good idea at all. I believe you have the order of operations backwards. You don't destroy the stream and then terminate the operation. You are supposed to terminate the operation and once that is done then destroy the stream. This is how asio sockets work, for the reasons @ashtum gave.

---

## Comment 11

> Username: ecorm  
> Created at: 2024-01-13 17:01:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890593426  

> You are supposed to terminate the operation and once that is done then destroy the stream.  
  
Sorry, not `terminate`, but `cancel` like in [Asio socket destructors](https://live.boost.org/doc/libs/release/doc/html/boost_asio/reference/basic_stream_socket/_basic_stream_socket.html):  
  
> This function destroys the socket, cancelling any outstanding asynchronous operations associated with the socket as if by calling `cancel`.

---

## Comment 12

> Username: ecorm  
> Created at: 2024-01-13 17:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890598098  

The way it is now with `websocket::stream`, async handlers must capture a `shared_ptr` ref count of the object holding the `websocket::stream`, in addition to the object holding the buffer (if those holder objects are not the same). If not with `shared_ptr` ref counts, then the application has the burden of making sure the `websocket::stream` is not destroyed while there are async operations pending.  
  
With Asio sockets, async handlers only need to capture the a `shared_ptr` ref count of the object holding the buffer. If the Asio socket is destroyed while async operations are pending, no undefined behavior occurs as long as the buffers still exist until the handler is invoked with an `operation_aborted` error code.  
  
If `websocket::stream` were to `cancel()` upon destruction like Asio sockets do, then that removes the burden of the async operation caller making sure the `websocket::stream` remains alive for the duration of the async operation.  
  
I hope I am making sense.

---

## Comment 13

> Username: ecorm  
> Created at: 2024-01-13 17:28:16 UTC  
> Updated at: 2024-01-13 17:28:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890601120  

Also note that [close()](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/basic_stream_socket/close/overload1.html) for Asio sockets also triggers immediate cancellation:  
  
> This function is used to close the socket. Any asynchronous send, receive or connect operations will be **cancelled immediately**, and will complete with the `boost::asio::error::operation_aborted` error.  
  
But, for whatever reason, you chose `websocket::stream::close` to have different semantics than Asio sockets, where it _initiates_ a new async operation. Thus my suggestion for a new `websocket::stream::terminate` function that more closely matches the Asio socket `close` semantics.  
  
I can understand why you chose `close` as the name of the function that sends a Websocket Close frame, but as an Asio network programmer, I am used to `close` meaning "stop everthing!" instead of meaning "start a new asynchronous operation".

---

## Comment 14

> Username: vinniefalco  
> Created at: 2024-01-13 22:11:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2798#issuecomment-1890780908  

Yes that is true about the name of `close`. Hopefully the name `async_close` caused less confusion :)   
  
Anyway if I do websockets again I will not be implementing the synchronous interface.
