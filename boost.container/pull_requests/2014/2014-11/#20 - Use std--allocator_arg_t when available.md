# #20 Use std::allocator_arg_t when available [Closed]

> Username: robert-matusewicz  
> Created at: 2014-11-16 18:40:50 UTC  
> Updated at: 2016-11-26 16:45:26 UTC  
> Closed at: 2016-11-26 16:45:26 UTC  
> Url: https://github.com/boostorg/container/pull/20  

Use std::allocator_arg_t when available.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-01-17 18:17:00 UTC  
> Url: https://github.com/boostorg/container/pull/20#issuecomment-70377652  

In the commit...  
  
---  
  
"Typedef'ed allocator_arg_t as "const std::allocator_arg_t &" to improve interoperability between C++11 allocator-aware types."  
  
https://github.com/boostorg/container/commit/7635eba375c2eda18307db78515cce0adc76c9c7  
  
---  
  
...I've typedef'ed allocator_arg_t as "const std::allocator_arg_t &" and updated "allocator_arg" accordingly. As std::allocator_arg_t is forward declared there is no dependency on <memory> which was my main concern.  
  
Do you think this commit would fix this issue?

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2016-11-26 16:45:26 UTC  
> Url: https://github.com/boostorg/container/pull/20#issuecomment-263073219  

Closed, commit:  
  
https://github.com/boostorg/container/commit/7635eba375c2eda18307db78515cce0adc76c9c7  
  
seems to correct the issue.

---
