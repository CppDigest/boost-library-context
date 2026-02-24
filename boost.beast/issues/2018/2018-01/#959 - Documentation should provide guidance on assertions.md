# #959 - Documentation should provide guidance on assertions [Closed]

> Username: eirslett  
> Created at: 2018-01-01 19:49:44 UTC  
> Updated at: 2024-02-14 07:50:13 UTC  
> Closed at: 2024-02-14 07:50:13 UTC  
> Url: https://github.com/boostorg/beast/issues/959  

I'm using Websockets (async) and doing a couple of read/writes against the server - maybe this is a race condition of some sort?  
  
```  
Assertion failed: (! base_), function emplace, file /usr/local/cmake/../include/boost/beast/websocket/detail/pausation.hpp, line 213.  
```  
  
This happens when I use beast bundled with boost 1.66.0.  
  
It's really hard to nail down where it comes from... I cannot seem to make a reproducible example.  
  
Looking at the Beast source code, it looks like this assertion fails:  
  
```c++  
template<class F>  
void  
pausation::emplace(F&& f)  
{  
    using type = holder<typename std::decay<F>::type>;  
    static_assert(sizeof(buf_type) >= sizeof(type),  
        "buffer too small");  
    BOOST_ASSERT(! base_);  
    base_ = ::new(buf_) type{std::forward<F>(f)};  
}  
```  
  
So wherever `base_` comes from, it has been set, even though it's not supposed to be set?  
Does that give me a clue I can use to find my problem?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-01 20:48:48 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354675497  

This could mean one of a few things:  
  
1. You are not protecting the stream with an implicit or explicit strand  
2. You are attempting to access the stream concurrently (streams and sockets are *not* thread safe)  
3. You are issuing more than one simultaneous read or write operation before waiting for a completion.  
  
See:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-01-01 20:49:12 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354675512  

We should document the asserts

---

## Comment 3

> Username: eirslett  
> Created at: 2018-01-02 00:13:10 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354687141  

Thanks! Assuming I want to dispatch two async_write from the same function, how can I make sure that the second write doesn't start until the first one is done?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-02 00:24:18 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354687778  

> how can I make sure that the second write doesn't start until the first one is done?  
  
You know the answer to that! You have to queue them up. How you do that is up to you.

---

## Comment 5

> Username: eirslett  
> Created at: 2018-01-02 00:27:56 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354687985  

You're right! I was kind of hoping there would exist a one-liner or a utility for making simple queues like that... I guess I'll have to cook up something then.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-01-02 00:41:20 UTC  
> Updated at: 2018-01-02 00:45:22 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354688585  

The javadocs for the write functions should state explicitly that you can only perform one write at a time, and the same for the read javadocs.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-01-02 00:45:58 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354688874  

>Assuming I want to dispatch two async_write from the same function, how can I make sure that the second write doesn't start until the first one is done?  
  
Is this a confession? :)

---

## Comment 8

> Username: eirslett  
> Created at: 2018-01-02 00:58:52 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354689627  

Touche!  
  
I'm using to doing `socket.send()` in JavaScript, the world is easier over there! I think JavaScript uses a queue under the hood. I assumed it would behave similarly with Beast, but to be fair, C++ is a more low-level ecosystem, so it makes sense. And of course I didn't read the docs... I just lazily copy-pasted from the example code. (The examples were so good!)  
  
Anyways, I got it to work. I have a "isWriting" boolean, a queue of message payloads, my send() function will push payload to the queue and call doWrite(). doWrite() will pop from the queue and ws.async_write() with an async callback, the async callback will set isWriting to false, check if the queue is empty, and if not, re-run doWrite().  
So you can close the issue if you want, unless you want to keep it open for a documentation issue...

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-01-02 01:03:51 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-354689892  

Glad to hear it got worked out. I'm imagining that people will build libraries on top of this. Someone might write a front end that manages the queues for you, deals with timeout pings, and the like. Perhaps it will be tailored for games. Or maybe another library will be tailored for interactive Javascript apps running in the browser. The sky is the limit.  
  
I'll keep this open since you found a rough corner that is worth smoothing, thanks.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-02-27 16:44:12 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-368943508  

I believe this is better addressed now with `soft_mutex` assertions:  
https://github.com/boostorg/beast/blob/d6c11a824a5fefa541ceb4d33300433c79ffa1a8/include/boost/beast/websocket/detail/stream_base.hpp#L85

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-02-27 16:47:40 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-368944722  

>I have a "isWriting" boolean, a queue of message payloads, my send() function will push payload to the queue and call doWrite(). doWrite() will pop from the queue and ws.async_write() with an async callback, the async callback will set isWriting to false, check if the queue is empty, and if not, re-run doWrite().  
  
This is a reasonable solution, and I would like to provide more clarity on why Beast does not perform queuing. It is a design philosophy of the library to avoid making decisions on behalf of the user when it can be avoided. In this case, a queue requires memory which means allocation. And some users would want to customize the allocator. If Beast provided a queue it would also need to provide an allocator. And it would need additional interfaces to control "backpressure", allowing TCP/IP flow control to propagate correctly to the application layer.  
  
There are many ways to implement a queue with backpressure, and some methods would perform better than others for particular applications. If Beast were to choose a method it would unavoidably disadvantage some users. Therefore, Beast avoids making this decision and leaves it up to the caller. If there is a clear, popular solution to this problem then it is likely someone would build a library on top of Beast to implement it, and that library would become greatly used. I think this is the correct separation of concerns, and I look forward to seeing what people will write with Beast.

---

## Comment 12

> Username: ashtum  
> Created at: 2024-02-14 07:50:13 UTC  
> Url: https://github.com/boostorg/beast/issues/959#issuecomment-1943236939  

Addressed in https://github.com/boostorg/beast/commit/ab32400d0046721af0d422b3feb6091486daa68d and https://github.com/boostorg/beast/commit/c5d8c7b64c2b3dc6eefdb03497b6e475bde93a52.
