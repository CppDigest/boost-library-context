# #136 define BOOST_NO_AUTO_PTR when building with libc++ and C++17 [Merged]

> Username: mclow  
> Created at: 2017-05-19 18:22:26 UTC  
> Updated at: 2017-05-20 10:28:11 UTC  
> Merged at: 2017-05-20 10:28:11 UTC  
> Closed at: 2017-05-20 10:28:11 UTC  
> Url: https://github.com/boostorg/config/pull/136  

New libc++ versions remove `std::auto_ptr` when building as C++17, unless the magic tag `_LIBCPP_ENABLE_CXX17_REMOVED_AUTO_PTR` is defined. Tell the rest of Boost that there's no `auto_ptr` in that case.

---

## Comment 1

> Username: mclow  
> Created_at: 2017-05-19 18:23:15 UTC  
> Url: https://github.com/boostorg/config/pull/136#issuecomment-302776248  

Just to be clear - this is Peter Dimov's idea. I think it's great, but don't want to take credit.

---
