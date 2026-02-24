# #322 - Question: why synchronized_value is still experimental? [Open]

> Username: myxo  
> Created at: 2020-07-05 22:04:56 UTC  
> Updated at: 2024-09-09 14:04:24 UTC  
> Url: https://github.com/boostorg/thread/issues/322  

Hi.  
Boost documentation says that Synchronized Values and Synchronized Queues are experimental features:  
https://www.boost.org/doc/libs/1_73_0/doc/html/thread/sds.html  
  
But synchronized_value.hpp wasn't change for some time already. I am wondering why is that? From a first glance I don't see any problems with current implementation. Is it really  an experimental feature?  
  
Well... Basically my question is: can I use it in production safely?

---

## Comment 1

> Username: petermost  
> Created at: 2024-09-09 14:04:23 UTC  
> Url: https://github.com/boostorg/thread/issues/322#issuecomment-2338219873  

I also want to use it ([Sourcetrail](https://github.com/petermost/Sourcetrail)) and I'm asking myself the same question, is it safe to use?
