# #103 Replacing static_casts with numeric_casts for narrowing. [Closed]

> Username: KevinHopps  
> Created at: 2019-02-07 02:22:45 UTC  
> Updated at: 2019-04-15 02:39:24 UTC  
> Closed at: 2019-04-15 02:39:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/103  



---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-14 19:10:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/103#issuecomment-483043180  

I don't think this is correct, at least not everywhere. There are places where an exception must not be thrown (when `error_code` is used to report errors).  
  
Also, I'm not fond of introducing a dependency on Boost.NumericConversion in Boost.Filesystem. If we are going to check for overflows, I'd like it to be done locally.

---

## Comment 2

> Username: KevinHopps  
> Created_at: 2019-04-15 02:39:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/103#issuecomment-483088807  

@Lastique, I agree. Exceptions should not be thrown from a function that reports errors through an error code.

---
