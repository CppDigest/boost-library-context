# #26 - Using  stackless coroutine instead of stateful one. [Closed]

> Username: enum-class  
> Created at: 2022-09-19 08:10:02 UTC  
> Updated at: 2022-09-21 03:11:53 UTC  
> Closed at: 2022-09-21 03:11:53 UTC  
> Url: https://github.com/boostorg/redis/issues/26  

I have found that you use stateful version of coroutine in codes. (async_compose).  
Is there any special technical decision about using stackful against stackless coroutines ?

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-09-19 20:39:44 UTC  
> Url: https://github.com/boostorg/redis/issues/26#issuecomment-1251524961  

Hi, I actually use stackless coroutines throughout my code as you can see e.g. [here](https://github.com/mzimbres/aedis/blob/fd82204ba9e6e85b1011c96ebff79539c4227ddf/include/aedis/detail/connection_ops.hpp#L36). As far as I can see `async_compose` is about simplifying the writing of stateful composed operations, that means, it is coroutine agnostic. In other words, you are free to use whatever completion token you like, including stackful coroutines like those provided by asio.
