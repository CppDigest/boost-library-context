# #1687 Fix signed/unsigned mismatch in file_stdio::seek [Closed]

> Username: achartier  
> Created at: 2019-08-23 19:58:18 UTC  
> Updated at: 2019-08-29 21:41:59 UTC  
> Closed at: 2019-08-29 21:41:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1687  

This fixes a C4388 warning when compiling with MSVC.

---

## Review 1 [Commented]

> Username: basiliscos  
> Created_at: 2019-08-24 18:58:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1687#pullrequestreview-279307660  

📁 include/boost/beast/core/impl/file_stdio.ipp

```diff
 243 |     }
 244 |-     if(offset > (std::numeric_limits<long>::max)())
 244 |+     if(offset > (std::numeric_limits<unsigned long>::max)())
```

> Username: basiliscos  
> Created_at: 2019-08-24 18:58:34 UTC  
> Updated_at: 2019-08-25 15:21:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1687#discussion_r317371496  

I think it should be `std::uint64_t` instead of `unsigned long`

> Username: vinniefalco  
> Created_at: 2019-08-25 03:38:33 UTC  
> Updated_at: 2019-08-25 15:21:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1687#discussion_r317381121  

Hmm... I don't think so. stdio (e.g. `FILE*`) only supports 32 bit offsets: https://en.cppreference.com/w/cpp/io/c/fseek  
If you want support for large files you have to use one of the platform-specific implementations.

> Username: basiliscos  
> Created_at: 2019-08-25 09:19:25 UTC  
> Updated_at: 2019-08-25 15:21:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1687#discussion_r317389502  

But if the input argument type to seek is `std::uint64_t` why should we care?   
  
The warning is quite annoying with `-Wall` flag.

> Username: achartier  
> Created_at: 2019-08-25 15:24:53 UTC  
> Url: https://github.com/boostorg/beast/pull/1687#discussion_r317403275  

I misunderstood that check earlier. Updated the change to cast the `std::numeric_limits` value into a `std::uint64_t` instead.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2019-08-25 17:56:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1687#pullrequestreview-279346653  

📁 include/boost/beast/core/impl/file_stdio.ipp

```diff
 243 |     }
 244 |-     if(offset > (std::numeric_limits<long>::max)())
 244 |+     if(offset > static_cast<std::uint64_t>(std::numeric_limits<long>::max()))
```

> Username: vinniefalco  
> Created_at: 2019-08-25 17:56:31 UTC  
> Updated_at: 2019-08-25 17:56:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1687#discussion_r317408579  

This looks right now, thanks


---
