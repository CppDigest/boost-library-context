# #37 Fix for Trac #11957 : static_vector::max_size() [Closed]

> Username: scopeInfinity  
> Created at: 2016-02-07 10:32:30 UTC  
> Updated at: 2016-02-24 13:46:52 UTC  
> Closed at: 2016-02-24 13:46:52 UTC  
> Url: https://github.com/boostorg/container/pull/37  



---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-02-24 13:46:52 UTC  
> Url: https://github.com/boostorg/container/pull/37#issuecomment-188261700  

Thanks for the report. Attached patch is not correct as vector::max_size must go through allocator traits. I'm closing this issue as it's duplicated in Trac, and resolve the bug there:  
  
https://svn.boost.org/trac/boost/ticket/11957

---
