# #1391 - Thanks and ASIO question [Closed]

> Username: romange  
> Created at: 2018-12-27 20:34:22 UTC  
> Updated at: 2019-01-04 12:02:09 UTC  
> Closed at: 2019-01-04 12:02:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1391  

Hi Vinnie,  
  
I just wanted to tell thanks for your library. I've integrated it into a framework which I've built over ASIO with Fibers to provide HTTP (beast) and RPC backend with IOContext-per-thread threading model. The code is asynchronous thanks to Boost.Fibers scheduler which allows me to write asynchronous code while preserving synchronous code style (no callbacks, no futures). The framework is very efficient and allows me to reach 300,000 QPS per core (1M qps on my laptop with 4 cores, for example). Anyway, if you are curious it's [here](https://github.com/romange/gaia).   
  
I have a question about ASIO sockets, maybe you know the answer. It seems that the only way to learn when socket is in disconnected/error state is to call some action on it and get a result. But suppose I have a client socket that got disconnected 5 mins before. For the last 5mins my process did not need to do anything on this socket so it does not realize that it's disconnected. Only when it writes something it gets an error_code and can start reconnecting. Is there a way to get a signal from Asio socket earlier? If not, do you know how to workaround this issue and be always up-to-date regarding the state of the socket?

---

## Comment 1

> Username: djarek  
> Created at: 2018-12-27 21:56:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1391#issuecomment-450238110  

You can perform `socket.async_wait(boost::asio::ip::tcp::socket::wait_read, wait_handler);`, which will signal readiness if the socket becomes "readable" (either the socket has pending data or it has been closed).

---

## Comment 2

> Username: romange  
> Created at: 2018-12-27 22:04:24 UTC  
> Updated at: 2018-12-27 22:04:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1391#issuecomment-450239122  

Interesting! Could it be that `wait_error` is the better option for this scenario? In any case, you gave me the direction and I will check it out!

---

## Comment 3

> Username: djarek  
> Created at: 2018-12-28 00:28:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1391#issuecomment-450261464  

`wait_error` indicates there is OOB data pending on the socket, it does not necessarily signal "errors".

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-12-28 01:35:37 UTC  
> Updated at: 2018-12-28 01:35:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1391#issuecomment-450269093  

> For the last 5mins my process did not need to do anything on this socket so it does not realize that it's disconnected.  
  
I think you should always have a read outstanding, so that you can get timely notification of `error::eof` (that's TCP/IP shutdown's FIN packet). Or if you have an SSL stream then you can detect the protocol-level close_notify (also delivered via `error::eof`).

---

## Comment 5

> Username: romange  
> Created at: 2019-01-04 10:05:54 UTC  
> Updated at: 2019-01-04 10:06:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1391#issuecomment-451402808  

Thanks @vinniefalco  and @djarek  I rewrote my code to always have an outstanding pending read.  
In order to differentiate between EOF or valid read signal I had to follow up with `receive()`.   
I had 2 options:  
1. To use MESSAGE_PEEK for 1 byte just for sake of checking for EOF. Advantage: the control over socket reads stays in the upper layers outside of my socket wrapper but it always knows the correct state of the socket. The disadvantage: could be twice more system calls per read. In the extreme case, for each notification from async_wait, we issue MESSAGE_PEEK and then the real call from upper level calls the `read()` as well.      
2. Use the "real" `read()` in the wrapper to fetch the data optimistically into intermediate buffer even though noone from upper layer is interested yet in this data. Disadvantage: more complicated state machine in my code + more memory copies.  
I chose option (2) assuming that system calls are much more expensive that copying small blocks of memory.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-01-04 11:15:28 UTC  
> Updated at: 2019-01-04 11:18:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1391#issuecomment-451417298  

The Virgin 1-byte-read versus The Chad optimistic buffered read :) :) :)  
  
Option 2 is best by a long shot! Your instincts are correct. Note, I do this in Beast websocket.

---

## Comment 7

> Username: romange  
> Created at: 2019-01-04 12:01:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1391#issuecomment-451426014  

Had to google this reference! :) :)
