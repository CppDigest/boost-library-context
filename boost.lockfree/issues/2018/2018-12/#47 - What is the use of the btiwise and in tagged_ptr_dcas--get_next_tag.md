# #47 - What is the use of the btiwise and in tagged_ptr_dcas::get_next_tag? [Closed]

> Username: brunoczim  
> Created at: 2018-12-05 20:52:01 UTC  
> Updated at: 2018-12-06 04:35:55 UTC  
> Closed at: 2018-12-06 04:35:55 UTC  
> Url: https://github.com/boostorg/lockfree/issues/47  

Here  
https://github.com/boostorg/lockfree/blob/develop/include/boost/lockfree/detail/tagged_ptr_dcas.hpp#L107  
  
What is the use of such bitwise and? This seems to be a no-op for me. In first place, because overflow is well-defined for `size_t` (wrapping). Doing `x & ~(size_t)0` is always `x`. In second place, because even if the overflow were not defined, the bitwise and would happen *after* the overflow, and UB would be invoked anyway.

---

## Comment 1

> Username: timblechmann  
> Created at: 2018-12-06 04:35:55 UTC  
> Url: https://github.com/boostorg/lockfree/issues/47#issuecomment-444745582  

msvc  
https://github.com/boostorg/lockfree/commit/85fd0b135677e62a140f08bb169cbc7f78a7f8e1
