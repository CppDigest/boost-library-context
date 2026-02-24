# #107 Test/skynet async [Merged]

> Username: brandon-kohn  
> Created at: 2017-01-12 15:42:13 UTC  
> Updated at: 2017-01-13 06:48:32 UTC  
> Merged at: 2017-01-13 06:48:32 UTC  
> Closed at: 2017-01-13 06:48:32 UTC  
> Url: https://github.com/boostorg/fiber/pull/107  

This worked against the 1.63.0 tag, but fails against develop with:  
  
> Thread 3 "skynet_async" received signal SIGSEGV, Segmentation fault.  
[Switching to Thread 0x7ffff5e9c700 (LWP 21713)]  
boost::intrusive::list_node_traits<void*>::set_next (next=<optimized out>,   
    n=<optimized out>) at ../../../../boost/intrusive/detail/list_node.hpp:66  
66	   {  n->next_ = next;  }  
(gdb)   
  
Still, I figured you might want the test as it mimics how HPX tests skynet. I wrote a bench in HPX that does skynet in the original way (like fiber does etc.) and fiber is at least two times faster in each case (windows and linux all with tcmalloc and for both the async test and for the explicit hpx/fiber thread case.) I wonder why? Perhaps it's the type of stack allocator?

---

## Comment 1

> Username: olk  
> Created_at: 2017-01-12 16:49:16 UTC  
> Url: https://github.com/boostorg/fiber/pull/107#issuecomment-272216357  

thx

---

## Comment 2

> Username: olk  
> Created_at: 2017-01-13 05:33:45 UTC  
> Updated_at: 2017-01-13 06:44:11 UTC  
> Url: https://github.com/boostorg/fiber/pull/107#issuecomment-272365012  

> fiber is at least two times faster in each case (windows  
> and linux all with tcmalloc and for both the async test  
> and for the explicit hpx/fiber thread case.) I wonder  
> why?  
  
difficult to say without profiling

---
