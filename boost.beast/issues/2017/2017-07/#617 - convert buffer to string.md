# #617 - convert buffer to string [Closed]

> Username: WeissGotsman  
> Created at: 2017-07-07 10:42:09 UTC  
> Updated at: 2017-07-08 05:33:54 UTC  
> Closed at: 2017-07-07 14:49:30 UTC  
> Url: https://github.com/boostorg/beast/issues/617  

the beast version 72 does not contain beast::to_string, then which api should I use to convert from beast::multi_buffer to std::string

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-07 12:37:50 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313669632  

`to_string` was provided as a way to display the contents of a buffer. For example:  
```  
multi_buffer b;  
...  
std:out << to_string(b.data());  
```  
The function sort of didn't belong in Beast because its expensive and there are other ways to accomplish the same result. For example, you can now write this instead:  
```  
std::out << buffers(b.data());  
```  
  
Alternatively you can use something other than a `multi_buffer`, such as a `flat_buffer`, and construct a `string_view` from it.  
  
What is your use-case?

---

## Comment 2

> Username: WeissGotsman  
> Created at: 2017-07-07 13:17:14 UTC  
> Updated at: 2017-07-07 13:21:46 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313678008  

Ah, I just found the beast::buffers api right after my post.   
  
One of my use case is performance critical: streaming real-time big data up to 20Mbps from several Websocket servers and then parsing them for analysis. So fast memory operations are preferred

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-07 13:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313680352  

Does your parser require a single contiguous input or can it work with sequence of multiple buffers?

---

## Comment 4

> Username: WeissGotsman  
> Created at: 2017-07-07 13:35:43 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313682405  

it works with sequence of multiple buffers.  
  
It's a series of json or quasi json data

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-07 13:37:39 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313682861  

`multi_buffer` is good for you then. are there any bottlenecks in Beast? Are you using a profiler?

---

## Comment 6

> Username: WeissGotsman  
> Created at: 2017-07-07 13:56:50 UTC  
> Updated at: 2017-07-07 14:02:22 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313687544  

I'm using Beast in a "multi-threaded, async within each thread" fashion (both http and websocket clients), and so far I haven't noticed any bottleneck, the data streaming seems consistent.   
  
I do have done a http request per second performance comparison with wrk (https://github.com/wg/wrk), using http server crow (https://github.com/ipkn/crow) on a dual Xeon E5 2665 cpu machine, the performance is around half of wrk, roughly 25000 rps.  
  
multiple facts have been taken into accounts: only resolve host once, only reconnect on EOF or disconnection, minimum memory allocation during requests, etc.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-07-07 14:30:34 UTC  
> Updated at: 2017-07-07 14:31:10 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313698465  

Please let me know what I can do to help to make sure Beast does what you need!  
  
25K rps is no joke, I'm getting around 5K and I have a pretty powerful gaming rig although we're probably comparing apples to kiwis at this point.

---

## Comment 8

> Username: WeissGotsman  
> Created at: 2017-07-07 14:49:30 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313703612  

Sure, and glad to to see beast is evolving actively.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-07-07 16:49:42 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313734816  

Just wondering, have you starred the repo?

---

## Comment 10

> Username: WeissGotsman  
> Created at: 2017-07-08 04:34:06 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313832949  

I follow you instead.. ;)  
  
vinnie, focus on your boost incubator, once admitted, it could bring you 10x stars!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-07-08 05:33:54 UTC  
> Url: https://github.com/boostorg/beast/issues/617#issuecomment-313835414  

LMAO
