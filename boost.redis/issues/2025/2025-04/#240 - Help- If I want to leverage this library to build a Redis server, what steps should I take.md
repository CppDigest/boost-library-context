# #240 - Help:  If I want to leverage this library to build a Redis server, what steps should I take? [Closed]

> Username: zhixiongdu027  
> Created at: 2025-04-09 06:38:34 UTC  
> Updated at: 2025-04-10 09:58:30 UTC  
> Closed at: 2025-04-10 09:58:30 UTC  
> Url: https://github.com/boostorg/redis/issues/240  

As mentioned,  
I want to build a custom Redis server. In short, my goal is to act as the server to handle Redis commands from clients and persist data to a designated storage engine.  
  
I didn’t find any documentation in the boost/redis repo addressing this. Could someone advise on the viability of this approach or offer recommendations?  
  
many tks.

---

## Comment 1

> Username: criatura2  
> Created at: 2025-04-09 08:41:48 UTC  
> Url: https://github.com/boostorg/redis/issues/240#issuecomment-2788814479  

Hi, Boost.Redis is mostly about the client side but you can use some of its parts to implement server side functionality, for example, here you can see how to parse a wire data in RESP3 format into your own data structure: https://github.com/boostorg/redis/blob/f7b4ec291e6aadaaa9d02a5bc9f9a2bafc26e049/test/test_low_level_sync_sans_io.cpp#L176  
  
What you won't find in Boost.Redis is a way to serialize arbitrary data structures into RESP3 format, that would be required for server responses I guess.

---

## Comment 2

> Username: zhixiongdu027  
> Created at: 2025-04-09 12:10:15 UTC  
> Url: https://github.com/boostorg/redis/issues/240#issuecomment-2789488961  

Thank you for your answer. Based on what you said, I might not be able to use this library anymore.   
​
