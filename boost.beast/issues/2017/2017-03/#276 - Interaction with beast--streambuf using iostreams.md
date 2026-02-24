# #276 - Interaction with beast::streambuf using iostreams [Closed]

> Username: florianbehrens  
> Created at: 2017-03-02 13:02:14 UTC  
> Updated at: 2017-06-08 05:32:00 UTC  
> Closed at: 2017-06-08 05:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/276  

The chosen name `beast::streambuf` suggests that it would be usable with C++ iostreams but it seems not to be as it is not derived from `std::basic_streambuf`. Is there any way to do this - apart from using a fixed-size `boost::asio::streambuf` along with `beast::buffers_adapter`?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-03-02 13:06:59 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283648573  

You're making a strong case that I should rename `beast::basic_streambuf` and `beast::streambuf` to `beast::basic_dynabuf` and `beast::dynabuf`!! Since they both model **DynamicBuffer**. The reason they are called streambuf is because the original concept was called **StreamBuffer** but I changed it in order to match the Networking-TS.  
  
It was never the intention to interoperate with C++ iostreams, because that comes with it a lot of baggage from the legacy design of those standard library classes. I will do the renames.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-03-02 13:07:37 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283648704  

I'm not sure how `buffers_adapter` will help you, that just adapts a mutable buffer sequence to meet the requirements of **DynamicBuffer**.

---

## Comment 3

> Username: florianbehrens  
> Created at: 2017-03-02 13:55:09 UTC  
> Updated at: 2017-03-02 13:56:03 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283659185  

Thanks for your quick response!  
  
What would you do if you had a 3rd party parser library that requires a `std::istream` (or `std::string` or ContiguousIterator range) for parser input and wanted to interface that library with Beast Websockets?   
  
Is there any way around copying memory from a `beast::dynabuf` (aka `beast::streambuf`) (if non-contiguous) to a contiguous memory area? Maybe a custom `std::basic_streambuf` derived "`dynabufbuf`"?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-03-02 14:00:42 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283660502  

Would something like `flat_streambuf` here help?  
https://github.com/vinniefalco/Beast/blob/http/include/beast/core/flat_streambuf.hpp#L18

---

## Comment 5

> Username: florianbehrens  
> Created at: 2017-03-02 15:25:07 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283683816  

Well, yes. But isn't that essentially the same as `boost::asio::basic_streambuf` with reallocations whenever the buffer isn't large enough to fit a Websocket message in?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-03-02 15:27:34 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283684593  

Yes its very similar. You could use either one - does that solve your problem?

---

## Comment 7

> Username: florianbehrens  
> Created at: 2017-03-02 15:34:13 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283686576  

Yes, but I dislike the reallocations. If those happened too often (I guess only with multi-frame Websocket messages?) I would go and try whether the above mentioned `std::basic_streambuf` derivative approach ("`dynabufbuf`") is feasible.  
  
Anyway, thank you very much for your help!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-03-02 15:43:10 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-283689182  

The design of Beast is such that it allows you complete control over the memory allocation strategy. All you need to do is provide a suitable implementation of **DynamicBuffer**. Beast and Asio come with some implementations. If those implementations are not sufficient you can easily write your own user defined type that meets the requirements (see http://vinniefalco.github.io/beast/beast/ref/DynamicBuffer.html).  
  
If you know that your WebSocket messages are going to be under a certain size, you can create your own implementation of **DynamicBuffer** which offers the ability to pre-allocate storage and only allocate new storage if the size is exceeded.  
  
Websocket users should not make assumptions about framing. A websocket implementation is free to re-frame messages at any time. This means Beast, a proxy, a router, or the software at the other end of the websocket connection can change the framing (as long as the order of bytes is preserved).

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-08 05:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/276#issuecomment-307002901  

I've done a comprehensive renaming of the buffers and refinement of their interfaces and implementation. If you check the docs there is a whole section on the various buffers. I believe that this issue is resolved, but if you're still having trouble feel free to re-open it or create a new one - Thanks!
