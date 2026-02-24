# #1889 - Assertion failed in stream_base.hpp [Closed]

> Username: jprochazk  
> Created at: 2020-04-03 15:29:27 UTC  
> Updated at: 2022-02-19 06:54:35 UTC  
> Closed at: 2020-04-04 09:39:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1889  

Hello, I'm new to beast and async I/O in general.  
  
Boost version: 277  
  
When calling async_write, i get this assertion error:  
```  
Assertion failed: ! b_, file C:\<snip>\boost\beast\core\detail\stream_base.hpp, line 80  
```  
Following the lead, I find this:  
```  
explicit  
pending_guard(bool& b)  
    : b_(b)  
{  
    // If this assert goes off, it means you are attempting  
    // to issue two of the same asynchronous I/O operation  
    // at the same time, without waiting for the first one  
    // to complete. For example, attempting two simultaneous  
    // calls to async_read_some. Only one pending call of  
    // each I/O type (read and write) is permitted.  
    //  
    BOOST_ASSERT(! b_);  
    b_ = true;  
}  
```  
However, when this error occurs, there is no other on-going async_write. I store a bool in my websocket class to determine if another async_write can be dispatched.  
  
I tried to make a minimal reproducible example, but I could not reproduce the issue in my minimal app. I modified [the chat-multi example](https://www.boost.org/doc/libs/1_72_0/libs/beast/example/websocket/server/chat-multi/) to be as close to my application. I then went and compared my actual application to the minimal application.  
  
[Here](https://github.com/jprochazk/ws-game-server/blob/7a702487786699ae0bfa29fee9d4ef289c0cf9cf/server/src/network/websocket.cpp#L55) is the culprit in my actual application, and [here](https://github.com/jprochazk/beast-async-minimal/blob/759429e50be6369e18337bd67618b4421ef218f4/websocket_session.cpp#L100) is the same place in the minimal example. I can't find the bug.  
  
  
What else can I do to identify the issue?

---

## Comment 1

> Username: cmazakas  
> Created at: 2020-04-03 15:57:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1889#issuecomment-608521281  

This code seems to have very subtle race conditions.  
  
https://github.com/jprochazk/ws-game-server/blob/7a702487786699ae0bfa29fee9d4ef289c0cf9cf/server/src/network/websocket.cpp#L47  
  
Here you're reading a member of the websocket session but this is being done externally outside the websocket's associated executor.  
  
All the public member functions from the example websocket sessions need to simply call `asio::post` and then immediately return. Move the checks into the private member functions and then this code should Just Work.  
  
I'd recommend running this through tsan first and confirming the race conditions, though. But I am fairly confident this is the source of your errors.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-04-03 16:08:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1889#issuecomment-608527307  

`beast::websocket::stream` is _not thread-safe_.

---

## Comment 3

> Username: jprochazk  
> Created at: 2020-04-03 18:26:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1889#issuecomment-608592387  

Thank you for the reply.   
  
@LeonineKing1199 I never heard of "tsan" before, so I looked it up. From what I found, it's a part of clang to check for concurrency bugs. Unfortunately, it's not available on windows, and I couldn't get any alternatives to work.   
I removed the access to ```should_close_``` from ``` send()```. It doesn't change the outcome.  
  
@vinniefalco please, could you explain why that is causing my issue?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-04-03 19:10:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1889#issuecomment-608611145  

>  please, could you explain why that is causing my issue?  
  
It just my experience that in 100% of cases where the assert goes off, where the user did not explicitly call `async_write` twice in a row, that the reason is because of improper synchronization of the `stream` object. Either because an implicit or explicit strand is missing, or because of concurrent access from multiple threads.

---

## Comment 5

> Username: cmazakas  
> Created at: 2020-04-03 21:33:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1889#issuecomment-608690112  

@jprochazk You may wanna read this:  
https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/overview/core/strands.html  
  
The solution is to _not_ add random mutexes everywhere but make sure that all your public member functions do is call `asio::post(ws_.get_executor(), ...)` and return. This way you get passive thread-safety for free.

---

## Comment 6

> Username: jprochazk  
> Created at: 2020-04-04 09:39:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1889#issuecomment-609003508  

@LeonineKing1199 I've changed the [websocket](https://github.com/jprochazk/ws-game-server/blob/master/server/src/network/websocket.cpp)'s public interface to do that, but the issue persists. To me, it seems that the bug is outside the websocket class, but it's like looking for a needle in a haystack, and I've been looking at the haystack for too long.   
  
Fortunately, it's a personal project and I haven't gotten very far yet, so seeing as the minimal example is working, I will use it as a baseline to re-write my application's networking code. If I end up finding what was causing the issue, I'll make sure to write it here in case someone stumbles upon this when experiencing the same issue.  
  
Thank you for your time

---

## Comment 7

> Username: RajeshvShiyal  
> Created at: 2022-02-19 06:54:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1889#issuecomment-1045905453  

Thanks all of you. Because same issue I was facing since last few days. Now it got resolved by using boost::asio::post  
  
@jprochazk @vinniefalco @cmazakas  your above conversation solved my issue.
