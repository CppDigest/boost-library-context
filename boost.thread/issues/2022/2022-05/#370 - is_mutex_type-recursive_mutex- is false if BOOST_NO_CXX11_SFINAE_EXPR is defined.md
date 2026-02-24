# #370 - is_mutex_type<recursive_mutex> is false if BOOST_NO_CXX11_SFINAE_EXPR is defined [Open]

> Username: jwakely  
> Created at: 2022-05-10 13:22:17 UTC  
> Updated at: 2022-05-10 13:25:08 UTC  
> Url: https://github.com/boostorg/thread/issues/370  

This part of the old `has_member_try_lock` implementation fails to match `recursive_mutex::try_lock()` because it's `noexcept`:  
  
https://github.com/boostorg/thread/blob/4abafccff4bdeb4b5ac516ff0c2bc7c0dad8bafb/include/boost/thread/lockable_traits.hpp#L143-L146  
  
The bug is still there in the current code, but hidden by a35ffa3a83e6f74262d66feac66ef5b513c99745 which has a working version that is used by most compilers since Boost 1.69.0  
  
A fix would be to add another partial specialization for `noexcept` functions:  
  
```c++  
#ifndef BOOST_NO_CXX11_NOEXCEPT  
        template<typename U>  
        static true_type has_member(bool (U::*)() noexcept);  
#endif  
```

---

## Comment 1

> Username: jwakely  
> Created at: 2022-05-10 13:25:08 UTC  
> Url: https://github.com/boostorg/thread/issues/370#issuecomment-1122390449  

Demo of the bug in 1.66.0: https://godbolt.org/z/hnTrsceTv
