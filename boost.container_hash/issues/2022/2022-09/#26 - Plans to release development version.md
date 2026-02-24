# #26 - Plans to release development version [Closed]

> Username: andrewcorrigan  
> Created at: 2022-09-09 15:03:59 UTC  
> Updated at: 2022-09-09 15:14:58 UTC  
> Closed at: 2022-09-09 15:14:58 UTC  
> Url: https://github.com/boostorg/container_hash/issues/26  

I'm getting this error in Boost 1.80.0 with Clang/Libc++-15  
```  
/boost/boost/container_hash/hash.hpp:132:33: error: no template named 'unary_function' in namespace 'std'; did you mean '__unary_function'?  
        struct hash_base : std::unary_function<T, std::size_t> {};  
```  
It looks like this is already fixed here and has been since 2019, 504857692148d52afe7110bcb96cf837b0ced9d7 and the current version doesn't even have any `std::unary_function` code, even with a guard.   Is there a timeline for the version of `container_hash` in this repo to merged into the released version of Boost?

---

## Comment 1

> Username: pdimov  
> Created at: 2022-09-09 15:12:20 UTC  
> Url: https://github.com/boostorg/container_hash/issues/26#issuecomment-1242105302  

This commit is part of 1.80.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-09-09 15:14:03 UTC  
> Url: https://github.com/boostorg/container_hash/issues/26#issuecomment-1242107302  

The commit that fixes the libc++15 issue is https://github.com/boostorg/config/commit/f0af4a9184457939b89110795ae2d293582c5f66, and it's not in 1.80. It should be in 1.81.

---

## Comment 3

> Username: andrewcorrigan  
> Created at: 2022-09-09 15:14:58 UTC  
> Url: https://github.com/boostorg/container_hash/issues/26#issuecomment-1242108334  

Excellent, thank you for the quick response and clarification!  
  
Closing...
