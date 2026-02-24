# #2 - Is this ready? [Closed]

> Username: WillNilges  
> Created at: 2020-11-06 20:41:11 UTC  
> Updated at: 2020-11-09 15:56:50 UTC  
> Closed at: 2020-11-09 15:56:50 UTC  
> Url: https://github.com/boostorg/redis/issues/2  

I'm looking for a solid C++ redis client to use for an embedded project and I stumbled upon this. It looks pretty active, but young. Is this ready for production use?

---

## Comment 1

> Username: mzimbres  
> Created at: 2020-11-07 13:21:13 UTC  
> Url: https://github.com/boostorg/redis/issues/2#issuecomment-723445580  

I have been using it for over two years now and it is stable. However recently I begin to introduce some breaking changes in the api to support ssl, resp3 and the upcoming C++ asynchronous model that makes it possible to use it with coroutines, callbacks and futures. I am also planning non-allocating buffers and commands. It suits my needs currently.

---

## Comment 2

> Username: WillNilges  
> Created at: 2020-11-09 15:56:48 UTC  
> Url: https://github.com/boostorg/redis/issues/2#issuecomment-724102820  

Interesting, thanks for the info. Have a star, and I might be back.
