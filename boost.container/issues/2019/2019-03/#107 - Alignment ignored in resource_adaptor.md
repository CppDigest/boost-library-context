# #107 - Alignment ignored in resource_adaptor? [Closed]

> Username: vinniefalco  
> Created at: 2019-03-11 18:17:56 UTC  
> Updated at: 2019-05-05 00:07:07 UTC  
> Closed at: 2019-05-05 00:06:55 UTC  
> Url: https://github.com/boostorg/container/issues/107  

`resource_adaptor` rebinds the caller's _Allocator_ type to `char`, which destroys the alignment information. Then in `allocate` it ignores the `alignment` parameter. Is this the right thing to do?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-05-04 21:54:25 UTC  
> Url: https://github.com/boostorg/container/issues/107#issuecomment-489368556  

You are right. operator new is guaranteed to be aligned to any type without extended alignment attributes, but the char-rebound Allocator type could only guarantee byte alignment. I'll review design alternatives to guarantee aligned allocations.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-05-04 22:13:46 UTC  
> Updated at: 2019-05-04 22:14:33 UTC  
> Url: https://github.com/boostorg/container/issues/107#issuecomment-489369588  

I'm not really happy with the polymorphic allocator / memory resource design but that's not Boost.Container's problem. How do you implement a reference-counted memory resource? Seems there's no way.  
  
I had to roll my own as you can see here:  
https://github.com/vinniefalco/BeastLounge/blob/develop/include/boost/beast/_experimental/json/storage.hpp#L34

---

## Comment 3

> Username: igaztanaga  
> Created at: 2019-05-04 23:55:24 UTC  
> Url: https://github.com/boostorg/container/issues/107#issuecomment-489374436  

memory_resource isn't thought for that use case, many would not like to pay for a reference-counted design that they won't use ;-)

---

## Comment 4

> Username: igaztanaga  
> Created at: 2019-05-05 00:07:07 UTC  
> Url: https://github.com/boostorg/container/issues/107#issuecomment-489375053  

Thanks for the bug report!
