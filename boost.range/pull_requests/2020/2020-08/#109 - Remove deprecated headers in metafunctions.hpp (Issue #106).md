# #109 Remove deprecated headers in metafunctions.hpp (Issue #106) [Closed]

> Username: rogerorr  
> Created at: 2020-08-18 15:56:06 UTC  
> Updated at: 2020-09-16 11:00:04 UTC  
> Closed at: 2020-09-16 10:59:44 UTC  
> Url: https://github.com/boostorg/range/pull/109  

There are three headers included in this file which are deprecated.  
- They each pull in a header that is already included.  
- They each define a deprecated struct (`range_result_iterator`, `range_const_reverse_iterator`, and `range_reverse_result_iterator`, respectively.  
However:  
- boost itself does not rely on these structs being defined via inclusion of this header  
- users of boost can simply add a #include of the required file, or (better) change their usage to avoid the deprecated `struct`s.

---

## Comment 1

> Username: rogerorr  
> Created_at: 2020-09-16 10:59:44 UTC  
> Url: https://github.com/boostorg/range/pull/109#issuecomment-693331863  

This was fixed independently in 486c74f

---
