# #1752 - How to mock boost::beast::websocket::stream in Boost 1.71 [Closed]

> Username: JunielKatarn  
> Created at: 2019-11-01 09:17:28 UTC  
> Updated at: 2019-11-06 21:25:54 UTC  
> Closed at: 2019-11-06 21:25:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1752  

### Version of Beast  
  
266  
  
Question.  
  
We have a WebSocket client that relies on Beast (as of Boost 1.68).  
We want to upgrade to Boost 1.71 (Beast 266), and notice some APIs have changed.  
The client has a mockable subtype for unit testing, which skips any real TCP calls and just runs previously provided callbacks to set any result values as needed.  
  
Excerpt of the mockable client:  
```  
template <class RequestDecorator, class HandshakeHandler>  
BOOST_ASIO_INITFN_RESULT_TYPE(HandshakeHandler, void(error_code))  
MockStream::async_handshake_ex(  
    boost::string_view host,  
    boost::string_view target,  
    RequestDecorator const &decorator,  
    HandshakeHandler &&handler) {  
  BOOST_BEAST_HANDLER_INIT(HandshakeHandler, void(error_code));  
  post(  
      get_executor(),  
      bind_handler(std::move(init.completion_handler), HandshakeResult(host.to_string(), target.to_string())));  
  
  return init.result.get();  
}  
```  
  
In Beast 266, the macro `BOOST_ASIO_INITFN_RESULT_TYPE` is no longer available.  
From `async_handshake_ex`, it seems the new macros for declaring a WebSocked composed async operation are `BOOST_BEAST_ASYNC_RESULTx`, with the result produced by a call to `boost::asio::async_initiate` instead of being defined by macro `BOOST_BEAST_HANDLER_INIT`.  
  
The question:  
How can we rewrite those methods so they just `boost::async::post` the provided functor (`HandshakeResult()`)?  
  
Note, type `MockStream` is replaces `boost::beast::websocket::stream`, having:  
```  
BaseClient<StreamType<...>>  
|- RealClient<boost::beast::websocket::stream<...>>  
|- TestClient<MockStream>  
```  
  
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-01 12:50:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1752#issuecomment-548776502  

Why not just use `websocket<test::stream>` like what is done in the beast websocket tests?  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/_experimental/test/stream.hpp#L49  
  
Or even just regular sockets, connect them to each other using the loopback address?

---

## Comment 2

> Username: JunielKatarn  
> Created at: 2019-11-02 01:11:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1752#issuecomment-548995962  

> Why not just use `websocket<test::stream>` like what is done in the beast websocket tests?  
  
Thanks, I'm looking into it.

---

## Comment 3

> Username: JunielKatarn  
> Created at: 2019-11-02 12:12:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1752#issuecomment-549038228  

> Why not just use `websocket<test::stream>` like what is done in the beast websocket tests?  
>   
  
After trying it out, seems like `test::stream` lacks some methods that we need from `websocket::stream`, such as `set_option` or `async_connect` via its lowest layer. We want to use or implement something that has the same methods as `websocket::stream`, without making a real TCP connection (which leaves the loopback address option out).  
  
Our code works when using 1.68 as mentioned initially, we need to understand how to port it to 1.71.  
  
At this point, it seems this is not a Beast issue, but an issue with Asio's async_initiate usage.  
Boost documentation on it doesn't seem very robust: https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/async_initiate.html  
  
Do you have any resources where you learned how to use `async_initiate` in the `websocket::stream` implementation?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-11-02 13:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1752#issuecomment-549044213  

Yeah the docs for asio are... spartan. If you look in the commit log for the change which introduced `async_initiate` there should be some docs in there. And you can look at the asio source code to see examples of how it is used.

---

## Comment 5

> Username: JunielKatarn  
> Created at: 2019-11-06 21:25:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1752#issuecomment-550507296  

I have been able to resolve this by looking at the commit that introduces `async_initiate` and mimicking part of your actual implementation of `boost::beast::websocket::stream`.  
  
Posting my solution here, in the off case another developer needs to "emulate" `websocket::stream` in the future:  
  
```  
std::function<boost::system::error_code(std::string, std::string)> HandshakeResult;  
  
template <class HandshakeHandler>  
BOOST_ASIO_INITFN_RESULT_TYPE(HandshakeHandler, void(error_code))  
MockStream::async_handshake(  
    boost::string_view host,  
    boost::string_view target,  
    HandshakeHandler &&handler)  
{  
  return async_initiate<HandshakeHandler, void(error_code)>(  
    [](HandshakeHandler&& handler, MockStream* ms, string h, string t)  
    {  
      post(ms->get_executor(), bind_handler(std::move(handler), ms->HandshakeResult(h, t)));  
    },  
    handler,  
    this,  
    host.to_string(),  
    target.to_string()  
  );  
}  
```  
  
A similar pattern applies for pretty much all the stream class's `async_*` methods.
