# #217 - static_ adaptor document is not correct [Closed]

> Username: jiayuehua  
> Created at: 2021-04-26 14:06:33 UTC  
> Updated at: 2022-01-21 08:29:23 UTC  
> Closed at: 2022-01-21 08:29:23 UTC  
> Url: https://github.com/boostorg/hof/issues/217  

https://www.boost.org/doc/libs/1_76_0/libs/hof/doc/html/include/boost/hof/static.html say that // In C++ this class can't be static-initialized, because of the non-  
// trivial default constructor.   
But that's not true. https://godbolt.org/z/46KdEb3r3 .times3 can create without any problem.   
So what is the exactly problem static_ try to solve? Will you give a example that static_ can work but old code can't work.   
Thanks very much.

---

## Comment 1

> Username: pfultz2  
> Created at: 2021-05-26 16:23:03 UTC  
> Url: https://github.com/boostorg/hof/issues/217#issuecomment-848914054  

Its dynamically initialized not statically initialized.

---

## Comment 2

> Username: jiayuehua  
> Created at: 2021-05-30 06:00:32 UTC  
> Url: https://github.com/boostorg/hof/issues/217#issuecomment-850947156  

Does that mean static_ is used for constant initialization? https://en.cppreference.com/w/cpp/language/constant_initialization. Here times_function is not pod type, so static_ can be used constant initial it.

---

## Comment 3

> Username: jiayuehua  
> Created at: 2022-01-20 12:45:41 UTC  
> Url: https://github.com/boostorg/hof/issues/217#issuecomment-1017463254  

my bad. I close it.
