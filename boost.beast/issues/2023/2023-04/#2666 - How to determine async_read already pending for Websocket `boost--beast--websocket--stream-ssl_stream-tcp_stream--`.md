# #2666 - How to determine async_read already pending for Websocket `boost::beast::websocket::stream<ssl_stream<tcp_stream>>` ? [Closed]

> Username: raidenluikang  
> Created at: 2023-04-02 18:12:17 UTC  
> Updated at: 2024-01-30 16:00:46 UTC  
> Closed at: 2024-01-30 16:00:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2666  

I can't find similar question. By [documentation](https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_websocket/notes.html) call two or more simultaneous async_read is malformed code.   
  
So my question is that, how can determine that there  async_read was already performed or not ?  
  
Or more technical the `boost::beast::websocket::stream<ssl_stream<tcp_stream>>` class has some method which return `flag` or `status` of reading ?  
  
---  
I have second question: My project have two separate io_context and both run their `own` threads.  Websocket async ssl client class uses first io_context, and Http  async ssl Client uses second io_context.  But sometimes I get [ similar this errors.](https://github.com/boostorg/beast/issues/2404) .  
  
Websocket client  uses ` beast::websocket::stream<beast::ssl_stream<beast::tcp_stream>>`  ,  no uses asio::strand  
Http client uses `beast::ssl_stream<beast::tcp_stream>`.    , no uses asio::strand  
  
Should I use same io_context for both Websocket client and Http client ?    
  
### Version of Beast  
#define BOOST_BEAST_VERSION 322  
boost 1.78  
  
### Operation System  
  Ubuntu 20.04 64 bit.  
   
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-04-02 19:45:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1493424243  

The way to know that you have an `async_read` pending, is when the following two conditions are both true:  
  
1. you have previously issued an `async_read`  
2. your completion handler has not been called yet

---

## Comment 2

> Username: raidenluikang  
> Created at: 2023-04-02 20:12:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1493429599  

I can use `bool is_reading` flag variable,  and put `true` before `async_read` and put `false` after completion handler has been called.  
  
But I thought that, there may be exist similar flag inside beast streams. Because beast streams catch simultaneously calls inside.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-04-03 10:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1494083037  

You should not need the explicit flag. Why do you want to know if you are reading? The program should always be reading from the websocket anyway. That is, as soon as an `async_read` completes, you usually want to start another one.

---

## Comment 4

> Username: raidenluikang  
> Created at: 2023-04-03 10:53:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1494100997  

I have second question: My project have two separate io_context and both run their own threads. Websocket async ssl client class uses first io_context, and Http async ssl Client uses second io_context. But sometimes I gethttps://github.com/boostorg/beast/issues/2404 .  
  
Websocket client uses  beast::websocket::stream<beast::ssl_stream<beast::tcp_stream>> , no uses asio::strand  
Http client uses beast::ssl_stream<beast::tcp_stream>. , no uses asio::strand  
  
Should I use same io_context for both Websocket client and Http client ?

---

## Comment 5

> Username: raidenluikang  
> Created at: 2023-04-03 12:19:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1494221223  

```>>>Why do you want to know if you are reading? ```  
Because async_read  method is public and it  calls out-side of websocket class, depending on different cases  it may call sequencially or not call absolutly.  
I need catch async_read already pending, for out-side classes before call async_read.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2023-04-03 18:45:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1494804737  

if you structure your code correctly you will know

---

## Comment 7

> Username: raidenluikang  
> Created at: 2023-04-04 03:13:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1495284664  

ok, I can review my codes.

---

## Comment 8

> Username: ashtum  
> Created at: 2024-01-30 07:58:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1916266000  

@raidenluikang Is this still open?

---

## Comment 9

> Username: raidenluikang  
> Created at: 2024-01-30 15:52:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2666#issuecomment-1917315978  

@ashtum No, can you close it. Thanks.
