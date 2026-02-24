# #9 - Exceptions [Closed]

> Username: gizmo1904  
> Created at: 2018-05-08 15:13:46 UTC  
> Updated at: 2018-09-20 15:22:01 UTC  
> Closed at: 2018-09-20 15:22:01 UTC  
> Url: https://github.com/boostorg/contract/issues/9  

I was wondering if it's possible to use the library without exception support. I couldn't find any information on that and I can't compile with -fno-exceptions. Is it not possible?

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-05-08 15:58:05 UTC  
> Url: https://github.com/boostorg/contract/issues/9#issuecomment-387452624  

This won't work now. I will look if it is possible to add this for the next Boost release, but probably it won't be possible because the library uses exceptions internally to signal a contract assertion failure.  
  
In fact, any exception thrown from within functions/functors used to check preconditions, postconditions, etc. is handled by the library as a contract assertion failure. BOOST_CONTRACT_ASSERT does nothing more than throwing an exception with __FILE__ and __LINE__ info when its contract condition fails. Because of that, I don't think I can make the lib implementation work without exception support... but I'll look into it.  
  
Either way, I will document this.

---

## Comment 2

> Username: gizmo1904  
> Created at: 2018-05-09 10:57:22 UTC  
> Url: https://github.com/boostorg/contract/issues/9#issuecomment-387701991  

Okay, thanks for the clarification.

---

## Comment 3

> Username: lcaminiti  
> Created at: 2018-09-20 15:22:01 UTC  
> Url: https://github.com/boostorg/contract/issues/9#issuecomment-423224576  

This won't work because the library uses exceptions internally to signal contract failures.
