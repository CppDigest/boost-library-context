# #69 atomic_count_std_atomic: static_cast v to std::int_least32_t [Merged]

> Username: CJBussey  
> Created at: 2019-06-18 09:49:56 UTC  
> Updated at: 2019-06-23 11:21:53 UTC  
> Merged at: 2019-06-23 11:21:53 UTC  
> Closed at: 2019-06-23 11:21:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/69  

* fix implicit conversion warning

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-06-18 09:54:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/69#issuecomment-503034782  

It might be better to change the `long v` to `std::int_least32_t v` instead.

---

## Comment 2

> Username: CJBussey  
> Created_at: 2019-06-18 10:15:40 UTC  
> Updated_at: 2019-06-18 10:18:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/69#issuecomment-503042037  

Thanks for the quick response!  
  
I was initially thinking down those lines, but I settled on this fix for the following reasons:  
  
* The documentation for `detail::atomic_count` does state that the constructor arg should be convertible to long, regardless of implementation, as described in the header [include/boost/smart_ptr/detail/atomic_count.hpp](https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/detail/atomic_count.hpp#L24)  
* [atomic_count_gcc_x86](https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/detail/atomic_count_gcc_x86.hpp#L26) already uses the same strategy of static casting to the internal storage type  
  
That said, it could make sense to update the documentation to specify that the type is convertible to the implementation defined storage type and adjust the `atomic_count_gcc_x86` implementation. Would that be preferable?

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-06-18 11:58:20 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/69#issuecomment-503072607  

The use of `long` (instead of `int_least32_t`) in the documentation and the implementations was a mistake; the intent has always been to use a 32 bit counter, and now that it's common for `long` to be 64 bits this matters. But fixing that might have unintended consequences somewhere downstream.  
  
There's also the problem of `std::int_least32_t` being C++11; we can use `boost::int32_least_t` (in the documentation and the non-`std::atomic` implementations) for C++03 compatibility, but in the case both exist, there's no guarantee of them being the same. This could manifest in warnings.  
  
I'll think about it some, then probably apply your patch anyway. :-)

---
