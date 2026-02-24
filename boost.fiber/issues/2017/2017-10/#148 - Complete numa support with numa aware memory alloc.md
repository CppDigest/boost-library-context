# #148 - Complete numa support with numa aware memory alloc [Closed]

> Username: WeissGotsman  
> Created at: 2017-10-30 10:18:02 UTC  
> Updated at: 2018-01-22 09:20:42 UTC  
> Closed at: 2017-12-29 05:50:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/148  

please include cross platform numa aware memory alloc. It looks like it already exists on your another deprecated boost-job project.  
  
this is because on a numa system we may always need to set thread affinity for optimal locality of memory access, which means numa aware memory alloc is already there, and for the sake of completeness it's better for fiber to offer such support.  
  
Also, it's better to offer an interface that allow setting thread affinity outside the current thread, i.e. on a main thread scheduler.

---

## Comment 1

> Username: olk  
> Created at: 2017-12-29 05:50:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/148#issuecomment-354401236  

NUMA memory allocation is not portable ... you have greate difference in supported options (interleaved, strict ...).  
A alaternative is to set the default NUMA allocation policy for the process and use normal functions (malloc() etc.) for memory allocation - or - use platform specific NUMA functions.

---

## Comment 2

> Username: WeissGotsman  
> Created at: 2018-01-18 12:22:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/148#issuecomment-358630915  

No, your if else macro made it portable

---

## Comment 3

> Username: olk  
> Created at: 2018-01-22 09:20:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/148#issuecomment-359366057  

I'm too busy - but you are welcome to provide a pull-request.
