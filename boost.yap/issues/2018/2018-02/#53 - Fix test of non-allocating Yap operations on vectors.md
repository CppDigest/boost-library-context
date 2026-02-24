# #53 - Fix test of non-allocating Yap operations on vectors [Closed]

> Username: tzlaine  
> Created at: 2018-02-19 20:52:23 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-02-24 21:58:24 UTC  
> Url: https://github.com/boostorg/yap/issues/53  

vector_alloc_test.cpp:  
  
- Your replacement of operator new is technically  
  illegal, because it can return a null pointer.  
  
- I don't think the standard guarantees that vector makes  
  any specific number of allocations.  The correct  
  way is to set allocations = 0 after initializing  
  the vectors and then verify that no further allocations  
  happen.  Alternately, make operator new throw a bad_alloc  
  during the region where you expect no allocations.  
  As a bonus, it'll be easier to find the cause of  
  unexpected allocations, when they do happen.
